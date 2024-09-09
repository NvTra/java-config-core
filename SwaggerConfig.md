package com.tranv.d7shop.configurations;

import io.swagger.v3.oas.annotations.OpenAPIDefinition;
import io.swagger.v3.oas.annotations.enums.SecuritySchemeIn;
import io.swagger.v3.oas.annotations.enums.SecuritySchemeType;
import io.swagger.v3.oas.annotations.info.Info;
import io.swagger.v3.oas.annotations.security.SecurityScheme;
import io.swagger.v3.oas.annotations.servers.Server;
import org.springframework.context.annotation.Configuration;

@OpenAPIDefinition(
info = @Info(
title = "E-commerce api in Java Spring boot",
version = "1.0.0",
description = "Ứng dụng D7-Shop"
),
servers = {
@Server(url = "http://localhost:8080", description = "Local Development Server")
}
)
@SecurityScheme(
name = "bearer-key",
type = SecuritySchemeType.HTTP,
scheme = "bearer",
bearerFormat = "JWT",
in = SecuritySchemeIn.HEADER)
@Configuration
public class SwaggerConfiguration {

}
                <dependency>-->
<!--            <groupId>io.jsonwebtoken</groupId>-->
<!--            <artifactId>jjwt-api</artifactId>-->
<!--            <version>0.12.5</version>-->
<!--        </dependency>-->

<!--        <dependency>-->
<!--            <groupId>io.jsonwebtoken</groupId>-->
<!--            <artifactId>jjwt-impl</artifactId>-->
<!--            <version>0.12.5</version>-->
<!--            <scope>runtime</scope>-->
<!--        </dependency>-->
<!--        <dependency>-->
<!--            <groupId>io.jsonwebtoken</groupId>-->
<!--            <artifactId>jjwt-jackson</artifactId>-->
<!--            <version>0.12.5</version>-->
<!--            <scope>runtime</scope>-->
<!--        </dependency>