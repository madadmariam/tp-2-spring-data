# Gestion de produits - Spring Boot, JPA, H2

Application Spring Boot permettant de gérer des produits (CRUD) avec Spring Data JPA et une base de données H2 en mémoire.

## Démo

First Demo : https://www.youtube.com/watch?v=UXs39oVoaSs

## Technologies utilisées

- Java
- Spring Boot
- Spring Web
- Spring Data JPA
- H2 Database
- Lombok
- IntelliJ IDEA Ultimate

## Étapes de réalisation

### 1. Installer IntelliJ Ultimate

Télécharger et installer IntelliJ IDEA Ultimate : https://www.jetbrains.com/idea/

### 2. Créer un projet Spring Initializr

Créer un nouveau projet avec **Spring Initializr** et ajouter les dépendances suivantes :

- Spring Data JPA
- H2 Database
- Spring Web
- Lombok

### 3. Créer l'entité JPA `Product`

| Attribut   | Type     |
|------------|----------|
| `id`       | `Long`   |
| `name`     | `String` |
| `price`    | `double` |
| `quantity` | `int`    |

```java
package com.example.demo.entities;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Entity
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;
    private int quantity;
}
```

### 4. Configurer l'unité de persistance

Dans `src/main/resources/application.properties` :

```properties
spring.application.name=demo
server.port=8080

spring.datasource.url=jdbc:h2:mem:products-db
spring.datasource.username=sa
spring.datasource.password=

spring.h2.console.enabled=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

La console H2 est accessible sur : http://localhost:8080/h2-console

### 5. Créer l'interface JPA Repository (Spring Data)

```java
package com.example.demo.repository;

import com.example.demo.entities.Product;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.List;

public interface ProductRepository extends JpaRepository<Product, Long> {
    List<Product> findByNameContains(String keyword);
    List<Product> findByPriceGreaterThan(double price);
}
```

### 6. Tester les opérations de gestion de produits

Les tests se font dans la classe principale via un `CommandLineRunner` :

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    @Bean
    CommandLineRunner start(ProductRepository productRepository) {
        return args -> {
            // Ajouter des produits
            productRepository.save(new Product(null, "Ordinateur", 4500, 10));
            productRepository.save(new Product(null, "Imprimante", 1200, 5));
            productRepository.save(new Product(null, "Smartphone", 3000, 20));

            // Consulter tous les produits
            List<Product> products = productRepository.findAll();
            products.forEach(System.out::println);

            // Consulter un produit
            Product product = productRepository.findById(1L).orElse(null);
            if (product != null) {
                System.out.println(product);
            }

            // Chercher des produits
            productRepository.findByNameContains("o").forEach(System.out::println);
            productRepository.findByPriceGreaterThan(2000).forEach(System.out::println);

            // Mettre à jour un produit
            if (product != null) {
                product.setPrice(4000);
                productRepository.save(product);
            }

            // Supprimer un produit
            productRepository.deleteById(2L);
        };
    }
}
```

## Fonctionnalités

- [x] Ajouter des produits
- [x] Consulter tous les produits
- [x] Consulter un produit
- [x] Chercher des produits
- [x] Mettre à jour un produit
- [x] Supprimer un produit

## Lancer le projet

```bash
git clone https://github.com/ton-user/ton-repo.git
cd ton-repo
./mvnw spring-boot:run
```

Sous Windows :

```bash
mvnw.cmd spring-boot:run
```

## Structure du projet

```
src/main/java/com/example/demo
├── DemoApplication.java
├── entities
│   └── Product.java
└── repository
    └── ProductRepository.java
src/main/resources
└── application.properties
```

## Auteur

Ton Nom
