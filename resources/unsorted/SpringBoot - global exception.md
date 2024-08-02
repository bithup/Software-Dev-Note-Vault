# SpringBoot - global exception

## 全局异常的捕获和处理

```java
package org.bitoy.shimmer.adapter.controller.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.validation.BindException;
import org.springframework.validation.ObjectError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import javax.validation.ConstraintViolation;
import javax.validation.ConstraintViolationException;
import java.util.List;
import java.util.Set;
import java.util.stream.Collectors;

@Slf4j
@RestControllerAdvice
//@ControllerAdvice
public class GlobalExceptionHandler {
    /**
     * 
     * @param e
     * @return
     */
    @ResponseBody
    @ExceptionHandler(value = ConstraintViolationException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public String handle(ConstraintViolationException e) {
        if (log.isDebugEnabled()) log.debug(e.getMessage());
        Set<ConstraintViolation<?>> constraintViolations = e.getConstraintViolations();
        return constraintViolations.stream().map(ConstraintViolation::getMessage).collect(Collectors.joining("|"));
    }

    @ResponseBody
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public String handle(MethodArgumentNotValidException e) {
        List<ObjectError> objectErrors = e.getBindingResult().getAllErrors();
        return objectErrors.stream().map(ObjectError::getDefaultMessage).collect(Collectors.joining("|"));
    }

    @ResponseBody
    @ExceptionHandler(DemoException.class)
    public String handle(DemoException e) {
        return e.getMessage();
    }

    @ResponseBody
    @ExceptionHandler(BindException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public String handle(BindException e) {
        return e.getAllErrors().stream().map(ObjectError::getDefaultMessage).collect(Collectors.joining());
    }
}
```

@RestControllerAdvice和@ControllerAdvice的区别？
