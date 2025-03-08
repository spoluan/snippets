### **Spring Boot Banner**

Spring Boot provides a **banner feature** that displays a customizable ASCII art or text when the application starts. By default, Spring Boot includes a basic banner, but you can customize it to add your own branding or messages.

---

### **1. Default Spring Boot Banner**

When no custom banner is provided, Spring Boot displays the following default banner in the console:

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (vX.X.X)
```

- **Explanation**:
  - The version of Spring Boot being used is displayed in `(vX.X.X)`.
  - This banner is automatically included if no custom banner is configured.

---

### **2. Customizing the Banner**

You can replace the default banner by creating a **`banner.txt`** file and placing it in the `resources` directory of your project.

#### **Steps to Add a Custom Banner**:
1. **Generate Your Banner**:
   - Use an online ASCII art generator like [Bagill ASCII Signature Generator](https://bagill.com/ascii-sig.php) to create your custom banner.
   - Example banner generated from the link:
     ```
       ______                             _ _    ______      _                    
      / _____)                           | (_)  (_____ \    | |                   
     ( (____  _____ _   _ _____ ____   __| |_    _____) )__ | | _   _ _____ ____  
      \____ \| ___ | | | | ___ |  _ \ / _  | |  |  ____/ _ \| || | | (____ |  _ \ 
      _____) ) ____|\ V /| ____| | | ( (_| | |  | |   | |_| | || |_| / ___ | | | |
     (______/|_____) \_/ |_____)_| |_|\____|_|  |_|    \___/ \_)____/\_____|_| |_|
                                                                                  
     ```

2. **Save the Banner**:
   - Save the generated ASCII art as `banner.txt`.
   - Place the file in the `src/main/resources` directory of your Spring Boot project.

   **File Structure**:
   ```
   src/main/resources/
   ├── application.properties
   ├── banner.txt
   ```

3. **Run the Application**:
   - When the application starts, the content of `banner.txt` will be displayed in the console.

---

### **3. Disabling the Banner**

If you don't want any banner to be displayed, you can disable it in the `application.properties` file.

#### **Configuration**:
```properties
spring.main.banner-mode=off
```

---

### **4. Using Variables in the Banner**

Spring Boot allows you to include dynamic information (like application name, version, etc.) in the banner using placeholders.

#### **Supported Variables**:
- **`${application.version}`**: Application version.
- **`${application.formatted-version}`**: Formatted version.
- **`${spring-boot.version}`**: Spring Boot version.
- **`${spring.application.name}`**: Application name.

#### **Example**:
```txt
  ____  _            _    _   _      _ _ 
 | __ )| | ___   ___| | _| \ | | ___| | |
 |  _ \| |/ _ \ / __| |/ /  \| |/ _ \ | |
 | |_) | | (_) | (__|   <| |\  |  __/ | |
 |____/|_|\___/ \___|_|\_\_| \_|\___|_|_|

 :: Application Name :: ${spring.application.name}
 :: Spring Boot Version :: ${spring-boot.version}
```

---

### **5. Programmatically Modifying the Banner**

If you want to set or modify the banner programmatically, you can do so using the `SpringApplication` class.

#### **Code Example**:
```java
import org.springframework.boot.Banner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import java.io.PrintStream;

@SpringBootApplication
public class CustomBannerApplication {

    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(CustomBannerApplication.class);
        app.setBanner(new Banner() {
            @Override
            public void printBanner(Environment environment, Class<?> sourceClass, PrintStream out) {
                out.println("Custom Programmatic Banner!");
            }
        });
        app.run(args);
    }
}
```

---

### **6. Banner Modes**

Spring Boot provides three modes for the banner:

- **`console`** (default): Displays the banner in the console.
- **`log`**: Displays the banner in the log file.
- **`off`**: Disables the banner.

#### **Setting the Mode**:
```properties
spring.main.banner-mode=log
```

