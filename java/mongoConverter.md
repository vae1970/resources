#### mongo **converter**

```java
import org.springframework.core.convert.converter.Converter;
import org.springframework.data.convert.ReadingConverter;
import org.springframework.data.convert.WritingConverter;

/**
 * StatusEnumConverter
 *
 * @author vae
 */
public class StatusEnumConverter {

	@ReadingConverter
	public enum ReadStatusConverter implements Converter<Integer, StatusEnum> {
		/**
		 * INSTANCE
		 */
		INSTANCE;

		@Override
		public StatusEnum convert(Integer status) {
			return StatusEnum.getStatus(status);
		}
	}

	@WritingConverter
	public enum WriteStatusConverter implements Converter<StatusEnum, Integer> {
		/**
		 * INSTANCE
		 */
		INSTANCE;

		@Override
		public Integer convert(StatusEnum source) {
			return source.getStatus();
		}
	}
}

@Bean
@Override
public MongoCustomConversions customConversions() {
    DbRefResolver dbRefResolver = new DefaultDbRefResolver(factory);
    MongoCustomConversions conversions = new MongoCustomConversions(Arrays.asList(StatusEnumConverter.ReadStatusConverter.INSTANCE, StatusEnumConverter.WriteStatusConverter.INSTANCE));

    MongoMappingContext mappingContext = new MongoMappingContext();
    mappingContext.setSimpleTypeHolder(conversions.getSimpleTypeHolder());
    mappingContext.afterPropertiesSet();

    MappingMongoConverter converter = new MappingMongoConverter(dbRefResolver, mappingContext);
    converter.setCustomConversions(conversions);
    converter.afterPropertiesSet();
    return conversions;
}
```

