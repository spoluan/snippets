### **Spring Boot Banner**

Spring Boot offers a **banner feature** that displays a custom ASCII art message or text in the console when the application starts. This feature is customizable, and you can replace the default banner with your own.

---

### **1. Default Spring Boot Banner**

The default banner is displayed if no custom banner is provided. It looks like this:

```text
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::
```

---

### **2. Customizing the Banner**

You can replace the default banner with your own by creating a `banner.txt` file and placing it in the `resources` directory of your project.

#### **Steps to Add a Custom Banner:**
1. **Create a `banner.txt` File**:
   - Create a new file named `banner.txt` in the `src/main/resources` directory.
   - Add your desired ASCII art or text to this file.
   
2. **Example Custom Banner**:
   ```text
   ______                             _ _    ______      _                    
  / _____)                           | (_)  (_____ \    | |                   
 ( (____  _____ _   _ _____ ____   __| |_    _____) )__ | | _   _ _____ ____  
  \____ \| ___ | | | | ___ |  _ \ / _  | |  |  ____/ _ \| || | | (____ |  _ \ 
  _____) ) ____|\ V /| ____| | | ( (_| | |  | |   | |_| | || |_| / ___ | | | |
 (______/|_____) \_/ |_____)_| |_|\____|_|  |_|    \___/ \_)____/\_____|_| |_|
   ```

3. **Run the Application**:
   - When you start your Spring Boot application, the banner in `banner.txt` will replace the default one.

---

### **3. Generating a Custom Banner**

To create a custom ASCII art banner, you can use online tools like [Bagill ASCII Art Generator](https://bagill.com/ascii-sig.php). 

#### **Steps to Generate a Custom Banner**:
1. Open the [Bagill ASCII Art Generator](https://bagill.com/ascii-sig.php).
2. Enter your desired text (e.g., your application name or slogan).
3. Choose a font style and generate the ASCII art.
4. Copy the generated ASCII art and paste it into the `banner.txt` file.

---

### **4. Disabling the Banner**

If you don't want any banner to be displayed, you can disable it in the `application.properties` file:

```properties
spring.main.banner-mode=off
```

---

### **5. Modifying the Banner Programmatically**

You can also customize the banner programmatically by implementing the `org.springframework.boot.Banner` interface.

#### **Example Code**:
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
                out.println("Custom Banner: Welcome to My Application!");
            }
        });
        app.run(args);
    }
}
```

---

### **6. Dynamic Banner Variables**

You can include Spring Boot placeholders in your `banner.txt` file to dynamically display application information like version, name, etc.

#### **Supported Placeholders**:
- `${application.version}`: Displays the application version.
- `${application.formatted-version}`: Displays the formatted application version.
- `${spring-boot.version}`: Displays the Spring Boot version.

#### **Example**:
```text
:: My Custom Application ::
Version: ${application.version}
Spring Boot Version: ${spring-boot.version}
```

---

### **7. Where to Modify the Banner**

- **Default Location**: `src/main/resources/banner.txt`
- **Configuration File**: Modify settings in `application.properties` or `application.yml`.

#### **Properties for Banner Configuration**:
- **Enable/Disable Banner**:
  ```properties
  spring.main.banner-mode=console  # Options: console, log, or off
  ```
- **Custom Banner File Location**:
  ```properties
  spring.banner.location=classpath:custom-banner.txt
  ```
