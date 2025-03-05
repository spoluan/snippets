# Variable Handling in Spring Boot

Spring Boot allows flexible management of configuration variables using placeholders in `application.yml` or `application.properties`. This document explains how variables are detected and resolved.

---

## Placeholders in YAML

Variables in `application.yml` are defined using the `${VARIABLE_NAME:default_value}` syntax. Here's how Spring Boot resolves these placeholders:

1. **Environment Variables or System Properties**:
   - Spring Boot first looks for the variable in the system environment or JVM system properties.
   - Example:
     ```bash
     export IAM_HOST=https://example-host.com
     export COMMON_ADMIN_URL=https://example-admin.com
     ```

     Or, as JVM arguments:
     ```bash
     java -DIAM_HOST=https://example-host.com \
          -DCOMMON_ADMIN_URL=https://example-admin.com \
          -jar your-app.jar
     ```

2. **Default Value**:
   - If the variable is not found in the environment or system properties, Spring Boot uses the default value provided after the colon (`:`) in the placeholder.
   - Example:
     ```yaml
     iam:
       host: ${IAM_HOST:https://example.com}
     ```

     - If `IAM_HOST` is not defined, the value `https://example.com` is used.

---

## Example `application.yml`

Here’s an example of how variables are used in the `application.yml` file:

```yaml
spring:
  config:
    activate:
      on-profile: dit
    liquibase:
      enabled: true

iam:
  host: ${IAM_HOST:https://example.com}

cdk.security:
  commonAdminUrl: ${COMMON_ADMIN_URL:https://example/search/byLoginId.json}
  commonAdminUserDetailsUrl: ${iam.host}/rest/example/users/
```

### Explanation:
- **`iam.host`**:
  - Tries to resolve `IAM_HOST` from the environment or system properties.
  - Defaults to `https://example.com` if not found.

- **`cdk.security.commonAdminUrl`**:
  - Tries to resolve `COMMON_ADMIN_URL` from the environment or system properties.
  - Defaults to `https://example/search/byLoginId.json` if not found.

- **`cdk.security.commonAdminUserDetailsUrl`**:
  - References the resolved value of `iam.host` and appends `/rest/example/users/`.

---

## How to Override Configuration Variables

You can override these variables at runtime by:

1. Setting environment variables:
   ```bash
   export IAM_HOST=https://custom-1-host.com
   export COMMON_ADMIN_URL=https://custom-2-host.com
   ```

2. Passing JVM arguments:
   ```bash
   java -DIAM_HOST=https://custom-1-host.com \
        -DCOMMON_ADMIN_URL=https://custom-2-host.com \
        -jar your-app.jar
   ```
 
