---
ms.date: 02/22/2023
author: KarlErickson
ms.author: judubois
---

1. Create a new `Todo` Java class. This class is a domain model mapped onto the `todo` table that will be created automatically by Spring Boot. The following code ignores the `getters` and `setters` methods.

   ```java
   import org.springframework.data.annotation.Id;

   public class Todo {

       public Todo() {
       }

       public Todo(String description, String details, boolean done) {
           this.description = description;
           this.details = details;
           this.done = done;
       }

       @Id
       private Long id;

       private String description;

       private String details;

       private boolean done;

   }
   ```

1. Edit the startup class file to show the following content.

   ```java
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.boot.context.event.ApplicationReadyEvent;
   import org.springframework.context.ApplicationListener;
   import org.springframework.context.annotation.Bean;
   import org.springframework.data.repository.CrudRepository;

   import java.util.stream.Stream;

   @SpringBootApplication
   public class DemoApplication {

       public static void main(String[] args) {
           SpringApplication.run(DemoApplication.class, args);
       }

       @Bean
       ApplicationListener<ApplicationReadyEvent> basicsApplicationListener(TodoRepository epository) {
           return event->repository
               .saveAll(Stream.of("A", "B", "C").map(name->new Todo("configuration", congratulations, you have set up correctly!", true)).toList())
               .forEach(System.out::println);
       }

   }

   interface TodoRepository extends CrudRepository<Todo, Long> {

   }
   ```

   > [!TIP]
   > In this tutorial, neither the configurations nor the code have any authentication operations. In fact, connecting to Azure service requires authentication. In order to complete the authentication, you need to use the Azure Identity. Spring Cloud Azure uses `DefaultAzureCredential`, which Azure Identity provides to help you get credentials without any code changes.
   >
   > `DefaultAzureCredential` supports multiple authentication methods and determines which method should be used at runtime. For more information, see the [DefaultAzureCredential](../../sdk/identity-azure-hosted-auth.md#default-azure-credential) section of [Authenticate Azure-hosted Java applications](../../sdk/identity-azure-hosted-auth.md). `DefaultAzureCredential` enables your app to use different authentication methods in different environments (local vs. production) without implementing environment-specific code.
   >
   > In local development environments, you can use Azure CLI, Visual Studio Code, PowerShell, or other methods to complete the authentication. For more information, see [Azure authentication in Java development environments](../../sdk/identity-dev-env-auth.md). In Azure hosting environments, we recommend using managed identity to complete the authentication. For more information, see [What are managed identities for Azure resources?](/azure/active-directory/managed-identities-azure-resources/overview)

1. Start the application. The application stores data into the database. You'll see logs similar to the following example:

   ```shell
   2023-02-01 10:22:36.701 DEBUG 7948 --- [main] o.s.jdbc.core.JdbcTemplate : Executing prepared SQL statement [INSERT INTO todo (description, details, done) VALUES (?, ?, ?)]    
   com.example.demo.Todo@4bdb04c8
   ```
