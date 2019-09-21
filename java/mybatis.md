##### mybatis

1. mybatis-spring-boot

   必要的配置

   ```groovy
   mybatis:
   #  mapper.xml的包路径
     mapper-locations: classpath:mapper/*.xml
   #  entity的包路径
     type-aliases-package: com.continent.project.entity
   #  type-handler的包路径
     type-handlers-package: com.continent.project.typehandler
   ```

   

