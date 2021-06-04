# TaskSchedulerAndShedLockApplication


# Postgres
CREATE TABLE shedlock(name VARCHAR(64) NOT NULL, lock_until TIMESTAMP NOT NULL,
    locked_at TIMESTAMP NOT NULL, locked_by VARCHAR(255) NOT NULL, PRIMARY KEY (name));
    
-----------------------------------------------------------------------------------------    
    package com.codemer.taskSchedular;





import javax.sql.DataSource;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.scheduling.annotation.EnableScheduling;
import org.springframework.web.client.RestTemplate;
import net.javacrumbs.shedlock.provider.jdbctemplate.JdbcTemplateLockProvider;
import net.javacrumbs.shedlock.core.LockProvider;
import net.javacrumbs.shedlock.spring.annotation.EnableSchedulerLock;

@SpringBootApplication
@EnableScheduling
@EnableSchedulerLock(defaultLockAtMostFor = "PT30S")
public class TaskSchedulerAndShedLockApplication {

	@Bean
	public RestTemplate restTemplate() {
	    return new RestTemplate();
	}
	@Bean
    public LockProvider lockProvider(DataSource dataSource) {
        return new JdbcTemplateLockProvider(dataSource);
    }
	public static void main(String[] args) {
		SpringApplication.run(TaskSchedulerAndShedLockApplication.class, args);
		System.out.println("In the TASK schedular App it is running on port: 8086");
	}

}


----------------------------------------------------------------------------------


package com.codemer.taskSchedular.schedulars;

import java.text.SimpleDateFormat;
import java.util.Date;

import org.springframework.http.ResponseEntity;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import net.javacrumbs.shedlock.core.SchedulerLock;
@Component
public class TaskSchedulerToRunEveryday {
	
	 @Scheduled(cron = "0 0/1 * * * ?")
	    @SchedulerLock(name = "TaskScheduler_scheduledTask", lockAtLeastForString = "PT10M", lockAtMostForString = "PT14M")
	   public void fixedRateSch() {
	      SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
	      
	      
	      Date now = new Date();
	      String strDate = sdf.format(now);
	      System.out.println("Task Completed scheduler:: " + strDate);
	      
	   }

}


--------------------------------------------------------

server.port=8086
spring.datasource.url = jdbc:mysql://localhost:3306/nagsdb?useSSL=false
spring.datasource.username = root
spring.datasource.password = root


## Hibernate Properties
# The SQL dialect makes Hibernate generate better SQL for the chosen database
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5InnoDBDialect

# Hibernate ddl auto (create, create-drop, validate, update)
spring.jpa.hibernate.ddl-auto = update





-----------------------------------------------------------------------------



	<dependency>
            <groupId>net.javacrumbs.shedlock</groupId>
            <artifactId>shedlock-spring</artifactId>
            <version>2.2.0</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/net.javacrumbs.shedlock/shedlock-provider-jdbc-template -->
<dependency>
    <groupId>net.javacrumbs.shedlock</groupId>
    <artifactId>shedlock-provider-jdbc-template</artifactId>
    <version>2.1.0</version>
</dependency>

---------------------------------------------------------------------------------------
