# 🧪 **Atelier Jour 2 – Communication entre Microservices**

## 🎯 **Objectif**

Construire une application composée de plusieurs microservices qui communiquent entre eux via :

- Des appels REST
- Un annuaire Eureka
- Une API Gateway

Au final, tu auras une architecture complète avec les services suivants :

- **`user-service`** : Service principal exposant une liste d'utilisateurs
- **`name-service`** : Service secondaire appelé par `user-service` pour obtenir des noms
- **`eureka-server`** : Service de découverte pour gérer l'enregistrement et la découverte des services
- **`gateway-service`** : Service d'API Gateway pour centraliser les appels REST vers les autres services

---

## 🧩 **Architecture finale attendue**

```
[gateway-service] ---> [user-service] ---> [name-service]
                          ↑
                    (Service Discovery)
                          ↓
                    [eureka-server]
```

---

## **Étape 1️⃣ – Créer `user-service`**

1. Génère un projet Spring Boot via [https://start.spring.io](https://start.spring.io) :
   - Project : Maven
   - Java : 17
   - Dépendances : Spring Web, Eureka Discovery Client

2. Crée un contrôleur pour exposer une liste d'utilisateurs :
   ```java
   @RestController
   @RequestMapping("/users")
   public class UserController {
       @GetMapping
       public List<String> getUsers() {
           return List.of("user1", "user2");
       }
   }
   ```

3. Configure `application.properties` :
   ```properties
   server.port=8081
   spring.application.name=user-service
   eureka.client.service-url.defaultZone=http://localhost:8761/eureka
   ```

---

## **Étape 2️⃣ – Créer `name-service`**

1. Crée un projet similaire à `user-service` mais nomme-le `name-service`.

2. Crée un contrôleur pour exposer un endpoint `/names` :
   ```java
   @GetMapping("/names")
   public List<String> getNames() {
       return List.of("Alice", "Bob", "Charlie");
   }
   ```

3. Configure `application.properties` :
   ```properties
   server.port=8082
   spring.application.name=name-service
   eureka.client.service-url.defaultZone=http://localhost:8761/eureka
   ```

---

## **Étape 3️⃣ – Appeler `name-service` depuis `user-service`**

1. Ajoute la configuration pour un `RestTemplate` avec la gestion du load balancing grâce à Eureka. Crée une classe `@Configuration` dans `user-service` :
   ```java
   @Bean
   @LoadBalanced
   public RestTemplate restTemplate() {
       return new RestTemplate();
   }
   ```

2. Utilise ce bean dans le `UserController` pour appeler `name-service` :
   ```java
   @Autowired
   private RestTemplate restTemplate;

   @GetMapping
   public List<String> getUsers() {
       return restTemplate.getForObject("http://name-service/names", List.class);
   }
   ```

3. Démarre les services et vérifie que la communication fonctionne.

---

## **Étape 4️⃣ – Créer `eureka-server`**

1. Crée un projet Spring Boot avec la dépendance **Eureka Server**.
   
2. Active Eureka dans la classe `EurekaServerApplication` :
   ```java
   @EnableEurekaServer
   @SpringBootApplication
   public class EurekaServerApplication { ... }
   ```

3. Configure `application.properties` :
   ```properties
   server.port=8761
   spring.application.name=eureka-server
   eureka.client.register-with-eureka=false
   eureka.client.fetch-registry=false
   ```

4. Démarre `eureka-server`.

---

## **Étape 5️⃣ – Vérifier l’inscription des services**

1. Démarre `user-service` et `name-service`.

2. Accède au dashboard de Eureka via [http://localhost:8761](http://localhost:8761) pour vérifier que les services apparaissent correctement.

---

## **Étape 6️⃣ – Créer `gateway-service`**

1. Crée un nouveau projet Spring Boot avec les dépendances :
   - Spring Cloud Gateway
   - Eureka Discovery Client

2. Configure `application.yml` pour définir les routes :
   ```yaml
   server:
     port: 8080
   spring:
     application:
       name: gateway-service
   cloud:
     gateway:
       routes:
         - id: user-service
           uri: lb://user-service
           predicates:
             - Path=/users/**
         - id: name-service
           uri: lb://name-service
           predicates:
             - Path=/names/**
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:8761/eureka
   ```

3. Démarre `gateway-service`.

---

## **Étape 7️⃣ – Tester l’ensemble**

1. Lance tous les services :
   - `eureka-server`
   - `name-service`
   - `user-service`
   - `gateway-service`

2. Accède aux routes via **Gateway** :
   - `http://localhost:8080/users`
   - `http://localhost:8080/names`

3. Vérifie que les services communiquent correctement à travers l'API Gateway et que les services sont enregistrés dans Eureka.

---

## ✅ **Résultat final**

Tu as maintenant une architecture microservices complète avec :

- Communication inter-service via RestTemplate
- Enregistrement automatique des services avec Eureka
- API centralisée via Gateway

🎉 **Félicitations !** Tu as réussi à construire une architecture distribuée fonctionnelle. À toi de l'améliorer, d'ajouter de la sécurité, ou même de la dockeriser si tu le souhaites.

---

## 🧠 **Questions à réfléchir**

- Pourquoi utiliser une Gateway ?
- Que se passe-t-il si un service change de port ?
- Quelle est la différence entre un appel direct et un appel via la Gateway ?
- Quelle est l’utilité d’Eureka dans une architecture cloud ?