#### 自定义动态分组规则

```java
import com.vae1970.top.vo.RegisterVO;
import org.hibernate.validator.spi.group.DefaultGroupSequenceProvider;

import java.util.ArrayList;
import java.util.List;
public class RegisterGroupSequenceProvider implements DefaultGroupSequenceProvider<RegisterVO> {

    @Override
    public List<Class<?>> getValidationGroups(RegisterVO registerVO) {
        List<Class<?>> defaultGroupSequence = new ArrayList<>();
        defaultGroupSequence.add(RegisterVO.class);
        if (registerVO != null) {
            switch (registerVO.getUserType()) {
                case STUDENT:
                    defaultGroupSequence.add(StudentRegisterModifyVO.class);
                    break;
                case TEACHER:
                    defaultGroupSequence.add(TeacherRegisterModifyVO.class);
                    break;
                case PARENT:
                    defaultGroupSequence.add(ParentRegisterModifyVO.class);
                    break;
                default:
                    break;
            }
        }
        return defaultGroupSequence;
    }
}


```

```java
@GroupSequenceProvider(RegisterGroupSequenceProvider.class)
public class RegisterVO {
    
}
```

#### 自定义校验规则validator

```java
import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.*;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

/**
 * @author vae1970
 * @description 或条件匹配
 * <p>
 * 必须包含这个字段：private String oneMatch;
 */
@SuppressWarnings("unused")
@Target({ANNOTATION_TYPE, ElementType.METHOD, ElementType.FIELD, ElementType.CONSTRUCTOR, PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface OneMatch {

    /**
     * 校验的字段名
     *
     * @return 字段名
     */
    String validateFiledName();

    /**
     * 校验规则
     *
     * @return 规则
     */
    ValidateType validateType();

    /**
     * 正则表达式(当校验类型为正则时存在)
     *
     * @return 正则表达式
     */
    String regexp() default "";

    @Target({METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE})
    @Retention(RUNTIME)
    @Documented
    @Constraint(validatedBy = {OneMatchValidator.class})
    @interface List {
        OneMatch[] value();

        String message();

        Class<?>[] groups() default {};

        Class<? extends Payload>[] payload() default {};
    }
}
```

```java
import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;
import java.lang.reflect.Field;
import java.util.Optional;
import java.util.regex.Pattern;

/**
 * @author vae1970
 * @description 或条件校验
 */
public class OneMatchValidator implements ConstraintValidator<OneMatch.List, Object> {

    /**
     * 校验规则列表
     */
    private OneMatch[] matchList;

    /**
     * 返回的ErrorMessage
     */
    private String message;

    private static final String ERROR_MESSAGE_FIELD_NAME = "oneMatch";

    @Override
    public void initialize(OneMatch.List constraintAnnotation) {
        matchList = constraintAnnotation.value();
        message = constraintAnnotation.message();
    }

    @Override
    public boolean isValid(Object object, ConstraintValidatorContext constraintValidatorContext) {
        //  检查用于异常信息的必要字段
        try {
            object.getClass().getDeclaredField(ERROR_MESSAGE_FIELD_NAME);
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        }
        //  validate
        boolean validate = false;
        for (OneMatch oneMatch : matchList) {
            Object fieldValue = getValueByField(object, oneMatch.validateFiledName());
            boolean fieldValidate = false;
            switch (oneMatch.validateType()) {
                case REGULAR_EXPRESSION:
                    fieldValidate = Optional.ofNullable(fieldValue).map(String::valueOf).map(s -> Pattern.matches(oneMatch.regexp(), s)).orElse(false);
                    break;
                case NOT_NULL:
                    fieldValidate = fieldValue != null;
                    break;
                default:
            }
            //  验证失败的字段置空，防止脏数据产生漏洞
            if (!fieldValidate) {
                setValueByField(object, oneMatch.validateFiledName(), null);
            }
            validate |= fieldValidate;
        }
        if (!validate) {
            constraintValidatorContext.buildConstraintViolationWithTemplate(message).addPropertyNode(ERROR_MESSAGE_FIELD_NAME).addConstraintViolation();
        }
        return validate;
    }

    /**
     * 根据属性名称获取值
     *
     * @param object    java bean
     * @param fieldName 属性名称
     * @return 属性值
     */
    private static Object getValueByField(Object object, String fieldName) {
        try {
            if (object != null && fieldName != null) {
                Field field = object.getClass().getDeclaredField(fieldName);
                field.setAccessible(true);
                return field.get(object);
            }
        } catch (NoSuchFieldException | IllegalAccessException ignored) {
        }
        return null;
    }

    /**
     * 根据属性名称获取值
     *
     * @param object    java bean
     * @param fieldName 属性名称
     * @return 属性值
     */
    private static void setValueByField(Object object, String fieldName, Object value) {
        try {
            if (object != null && fieldName != null) {
                Field field = object.getClass().getDeclaredField(fieldName);
                field.setAccessible(true);
                field.set(object, value);
            }
        } catch (NoSuchFieldException | IllegalAccessException ignored) {
        }
    }
}
```

```java
@OneMatch.List(
        message = "请填写正确的手机号或邮箱账号",
        value = {@OneMatch(validateFiledName = "phoneNumber", validateType = REGULAR_EXPRESSION, regexp = RegularConstants.PHONE_REGEX),
                @OneMatch(validateFiledName = "email", validateType = REGULAR_EXPRESSION, regexp = RegularConstants.EMAIL_REGEX)}
        , groups = {ParentRegisterModifyVO.class, TeacherRegisterModifyVO.class})
public class RegisterVM {

}
```

