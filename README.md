# 🧩 Microservices Starter - Jour 2

Ce projet illustre une architecture **microservices distribuée** avec communication REST, **service discovery via Eureka**, et **API Gateway** avec Spring Cloud.

---

## 📦 Contenu du projet

- **eureka-server** : serveur d'annuaire (Service Registry)
- **user-service** : expose une API `/users` qui récupère des noms depuis `name-service`
- **name-service** : expose une API `/names` avec une liste de noms
- **gateway-service** : API Gateway qui route les requêtes vers `user-service` et `name-service`

---

## 🧭 Objectif pédagogique

- Comprendre la communication entre microservices via REST
- Mettre en place un annuaire de services avec **Eureka**
- Centraliser les appels API avec **Spring Cloud Gateway**
- Déployer une architecture complète avec ou sans Docker

---

## 🛠️ Lancement sans Docker (local IDE ou CLI)

1. **Pré-requis** :
   - Java 17
   - Maven
   - Docker (si test via conteneur en local)

2. Ouvrir 4 terminaux et exécuter les services dans cet ordre :

```bash
cd eureka-server
./mvnw spring-boot:run
```

```bash
cd name-service
./mvnw spring-boot:run
```

```bash
cd user-service
./mvnw spring-boot:run
```

```bash
cd gateway-service
./mvnw spring-boot:run
```

3. Tester les endpoints via Gateway :

- [http://localhost:8080/users](http://localhost:8080/users)
- [http://localhost:8080/names](http://localhost:8080/names)

---

## 🐳 Lancement avec Docker

1. Assure-toi que Docker est installé
2. Depuis la racine du projet :

```bash
docker-compose up --build
```

3. Attends que tous les services soient "UP"

4. Accède aux endpoints :

- Eureka Dashboard : [http://localhost:8761](http://localhost:8761)
- API Gateway vers `/users` : [http://localhost:8080/users](http://localhost:8080/users)
- API Gateway vers `/names` : [http://localhost:8080/names](http://localhost:8080/names)

---

## 📂 Structure simplifiée

```
microservices-jour2-full/
├── eureka-server/
├── gateway-service/
├── user-service/
├── name-service/
└── docker-compose.yml
```

---

## 📂 Structure simplifiée

```
.
|-- README.md
|-- docker-compose.yml
|-- eureka-server
|   |-- Dockerfile
|   `-- src
|       `-- main
|           |-- java
|           |   `-- com
|           |       `-- example
|           |           `-- eurekaserver
|           |               `-- EurekaserverApplication.java
|           `-- resources
|               `-- application.properties
|-- gateway-service
|   |-- Dockerfile
|   `-- src
|       `-- main
|           |-- java
|           |   `-- com
|           |       `-- example
|           |           `-- gatewayservice
|           |               `-- GatewayserviceApplication.java
|           `-- resources
|               `-- application.yml
|-- name-service
|   |-- Dockerfile
|   `-- src
|       `-- main
|           |-- java
|           |   `-- com
|           |       `-- example
|           |           `-- nameservice
|           |               |-- NameserviceApplication.java
|           |               `-- UserController.java
|           `-- resources
|               `-- application.properties
`-- user-service
    |-- Dockerfile
    `-- src
        `-- main
            |-- java
            |   `-- com
            |       `-- example
            |           `-- userservice
            |               |-- RestTemplateConfig.java
            |               |-- UserController.java
            |               `-- UserserviceApplication.java
            `-- resources
                `-- application.properties

---
```

## 💬 Exemple d'appel entre services

`user-service` appelle `name-service` via :

```java
List<String> names = restTemplate.getForObject("http://name-service/names", List.class);
```

La résolution du nom `name-service` est faite automatiquement grâce à **Eureka** et le **LoadBalancer** intégré.

---

## 👨‍🏫 Contexte pédagogique

Ce projet est utilisé dans une formation de 4e année pour introduire :
- Les architectures distribuées
- La communication inter-service REST
- Le rôle des composants Spring Cloud (Discovery, Gateway)
- Une première approche de déploiement avec Docker

---

🎉 Prêt à coder des microservices comme un pro !
