---
toc: true
title: " Spring Multiple Datasource Sample 예제"
description: "Spring CompletableFuture Sample 예제"
categories: [Spring]
tags: [Spring]
redirect_from:
  - /2019/07/12/
---

> Spring Multiple Datasource Sample 예제

### 설정 관련 설명 {#toc1}

```md
간단히 설명하지만, 매우매우 중요하니 잘 기억하기 바란다.
처음 Datasoure 에는 Bean 생성 객체들위에 @Primary 가 있어야만 한다. 나머지 것들에는 반드시 없어야 한다.
복붙하고나서 돌리면 에러가 Bean 생성 에러가 나와서 @Primary 가 없어야 동작한다는 것을 파악하기 어렵다.

패키지 구조도 매우 중요하다.
Ex) Entity 와 Repository 가 com.test.domain com.test.repo 라고 한다면 
지금 만드는 Config Java 파일의 package 구조는 com.test.config 이어야만 한다.
com.level1.level2.config 에 Config 파일 만들고
com.level1.level2.level3.domain
com.level1.level2.level3.repo 를 만들어 놓고 서버 돌리면
hikariCP, hibernate 설정이 먹지 않고 ConfigurationProperties 의 prefix 의 정보를 읽어서 Datasource 를 만든다.
정상적으로 서버가 떠서 잘 되는 구나 생각하겠지만, hikariCP 의 로깅을 DEBUG 로 해서 Connection Pool 설정
정보 로그를 보면 내가 설정한 정보는 먹지 않고 전부 default 로 세팅 되어 있음을 알 수 있다.

```

### Primary Datasource Java Sample {#toc2}

```java
import javax.persistence.EntityManagerFactory;
import javax.sql.DataSource;

import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.orm.jpa.EntityManagerFactoryBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.context.annotation.PropertySource;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.orm.jpa.JpaTransactionManager;
import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import com.zaxxer.hikari.HikariDataSource;

@Configuration
@PropertySource({ "classpath:application.yml" })
@EnableTransactionManagement
@EnableJpaRepositories(
 entityManagerFactoryRef = "firstEntityManagerFactory",
 transactionManagerRef = "firstTransactionManager",
 basePackages = {
  "com.mig.base.first.repo"
 }
)
public class FirstDatasourceConfig {
	 @Primary
	 @Bean(name = "firstDataSource")
	 @ConfigurationProperties(prefix = "spring.datasource")
	 public DataSource firstDataSource() {
		 HikariDataSource hikariDataSource = new HikariDataSource();

         return hikariDataSource;
//	  return DataSourceBuilder.create().build();
	 }

	 @Primary
	 @Bean(name = "firstEntityManagerFactory")
	 public LocalContainerEntityManagerFactoryBean
	 firstEntityManagerFactory(
	  EntityManagerFactoryBuilder builder,
	  @Qualifier("firstDataSource") DataSource dataSource
	 ) {
	  return builder
	   .dataSource(dataSource)
	   .packages("com.mig.base.first.pojo")
	   .persistenceUnit("first")
	   .build();
	 }

	 @Primary
	 @Bean(name = "firstTransactionManager")
	 public PlatformTransactionManager firstTransactionManager(
	  @Qualifier("firstEntityManagerFactory") EntityManagerFactory firstEntityManagerFactory
	 ) {
	  return new JpaTransactionManager(firstEntityManagerFactory);
	 }
}
```

### Secondary Datasource or more Java Sample {#toc3}

Secondary 또는 그 이상의 Datasource 에는 @Primary 가 없다.
붙이면 Bean 생성 관련 에러가 발생했던것으로 기억한다.
참고로 hibernate 와 hikariCP 의 설정을 application.yml 에 기록해도 동작하지 않아서
매우 오랜 검색 끝에 아래와 같이 설정하여 동작하는 소스를 만들었다.
찾기 어려움으로. properties 파일이나, yml 파일에 하나의 Datasource 가 있는게 아니라면
아래의 방식을 따르기로 나는 일단 마음 먹었다. 잊어버릴가봐 기록해둠.

```java
import java.util.Properties;

import javax.persistence.EntityManagerFactory;
import javax.sql.DataSource;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.jdbc.datasource.LazyConnectionDataSourceProxy;
import org.springframework.orm.jpa.JpaTransactionManager;
import org.springframework.orm.jpa.JpaVendorAdapter;
import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean;
import org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import com.google.common.collect.ImmutableMap;
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;

@Configuration
@ConfigurationProperties(prefix = "wony.datasource")
@PropertySource({ "classpath:application.yml" })
@EnableTransactionManagement
@EnableJpaRepositories(
 entityManagerFactoryRef = "wonyEntityManagerFactory",
 transactionManagerRef = "wonyTransactionManager",
 basePackages = { "com.mig.base.wony.repo" }
)
public class WonyDatasourceConfig extends HikariConfig {
	
	 @Bean(name = "wonyDataSource")
	 public DataSource wonyDataSource() {
		 
	    Properties dsProps = new Properties();
	    dsProps.put("driverClassName", "oracle.jdbc.OracleDriver");
	    dsProps.put("username", "WONY");
	    dsProps.put("password", "test");
	    dsProps.put("jdbcUrl", "jdbc:oracle:thin:@172.31.249.112:1023:TDWMES");
	    dsProps.put("connectionTimeout", 10000);// timeout 
        // 최대 Connection Pool 개수. 지금 남아있는 이슈가 하나 있는데, connection 을 close 하고 다시 pool 에 정상적으로
        //되돌려 주는 방법을 못찾겠다.
	    dsProps.put("maximumPoolSize", 30); 
        
//	    dsProps.put("idleTimeout", env.getProperty("hikari.idleTimeout", Integer.class));
//	    dsProps.put("maxLifetime", env.getProperty("hikari.maxLifetime", Integer.class));
//	    dsProps.put("leakDetectionThreshold", env.getProperty("hikari.leakDetectionThreshold", Integer.class));
//	    dsProps.put("jdbc4ConnectionTest", env.getProperty("hikari.jdbc4ConnectionTest", Boolean.class));

	    HikariConfig config = new HikariConfig(dsProps);
	    return new LazyConnectionDataSourceProxy(new HikariDataSource(config));
//		 return new LazyConnectionDataSourceProxy(new HikariDataSource(this));
//		 return DataSourceBuilder.create().build();
	 }

	 @Bean(name = "wonyEntityManagerFactory")
	 public EntityManagerFactory 
	 wonyEntityManagerFactory() {
	        JpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();

	        LocalContainerEntityManagerFactoryBean factory = new LocalContainerEntityManagerFactoryBean();
	        factory.setDataSource(this.wonyDataSource());
	        factory.setJpaVendorAdapter(vendorAdapter);
	        factory.setJpaPropertyMap(ImmutableMap.of(
	                "hibernate.dialect", "org.hibernate.dialect.Oracle12cDialect",
	                "hibernate.show_sql", "false",
	                "hibernate.jdbc.batch_size", "10", // 성능관련 옵션 batch_size 에 마춰서 동작하도록 개수 설정
	                "hibernate.jdbc.batch_versioned_data", "true",
	                "hibernate.order_inserts", "true"
	        ));
            // ImmutableMap 에는 최대 5개만 들어가서 2번 찍었음.
	        factory.setJpaPropertyMap(ImmutableMap.of(
                    "hibernate.order_updates", "true",
	                "hibernate.enable_lazy_load_no_trans", "true",
	                "hibernate.generate_statistics", "true" // 이걸 true 로 해놔야 insert 다건이 batch 형태로 돌아갔다는 통계 정보를 확인 할 수 있다. 중요함.
	        ));	        

	        factory.setPackagesToScan("com.mig.base.dpbas.pojo");
	        factory.setPersistenceUnitName("wony");
	        factory.afterPropertiesSet();

	        return factory.getObject();
	 }

    @Bean
    public PlatformTransactionManager wonyTransactionManager() {
        JpaTransactionManager tm = new JpaTransactionManager();
        tm.setEntityManagerFactory(wonyEntityManagerFactory());
        return tm;
    }	 
	 
}
```

### 설정들이 잘 되어 있는지 확인 하기 위한 logging setting {#toc4}

```yml
server:
  port: 8080

spring:
  application:
    name: test
    
  jpa:
    database: default
    properties:
      hibernate:
        show_sql: false
        meta_sql: false
    hibernate:
      ddl-auto: none
    show-sql: false      
    
  datasource:
    url: jdbc:oracle:thin:@IP_ADDR:PORT:XXX
    username: XXX
    password: XXX
    driver-class-name: oracle.jdbc.OracleDriver
    
wony:
  datasource:
    jdbc-url: jdbc:oracle:thin:@IP_ADDR:PORT:XXX
    username: XXX
    password: XXX
    driver-class-name: oracle.jdbc.OracleDriver    

logging:
  file: logs/application-debug.log
  pattern:
    console: "%d %-5level %logger : %msg%n"
    file: "%d %-5level [%thread] %logger : %msg%n"
  level:
    org.springframework.web: ERROR
    com.posco.mes3: DEBUG
    org.hibernate.engine: ERROR
    org.hibernate.validator: ERROR
    org.hibernate.persister: ERROR
    org.hibernate.boot: ERROR
    org.hibernate.cfg: ERROR
    org.hibernate.SQL: ERROR
    org.hibernate.loader: ERROR
    org.hibernate.event: ERROR
    org.hibernate: DEBUG
    com.zaxxer.hikari: DEBUG
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
