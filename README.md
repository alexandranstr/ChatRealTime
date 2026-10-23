# Arhitectura Sistemului de Chat Distribuit

## 1. Prezentare Generala

Proiectul consta in dezvoltarea unui backend de chat in timp real, bazat pe o arhitectura de microservicii. 

Sistemul va permite:
- Conversatii individuale si de grup.
- Trimiterea si primirea mesajelor in timp real prin WebSocket.
- Persistenta istoricului mesajelor.
- Confirmari de livrare si citire.
- Autentificare si autorizare bazata pe token-uri JWT.
- Scalabilitate si toleranta la erori.

## 2. Tehnologii Utilizate

| Tehnologie | Rol |
| --- | --- |
| Java 21 | Limbaj de programare |
| Spring Boot | Dezvoltarea microserviciilor |
| Spring Cloud Gateway | Rutarea cererilor si filtrare |
| Spring Cloud Netflix Eureka | Descoperirea dinamica a serviciilor |
| Spring Security | Securitate si autorizare |
| JWT | Autentificarea cererilor |
| Spring Data JPA | Acces la baza de date |
| PostgreSQL | Persistenta datelor |
| Spring WebSocket | Comunicare in timp real |
| STOMP | Protocol pentru mesagerie peste WebSocket |
| Docker Compose | Rularea infrastructurii si deploy local |
| Postman | Testarea API-urilor REST si a canalelor WebSocket |

## 3. Structura Generala a Proiectului

Proiectul este organizat in module Spring Boot independente (pattern-ul database-per-service):

```text
chat-system/
├── discovery-server/     # Eureka Service Discovery (Registry)
├── api-gateway/          # Spring Cloud Gateway (Rutare și Securitate)
├── auth-service/         # Microserviciu de Autentificare și Utilizatori
├── chat-service/         # Microserviciu de Chat și Mesagerie în timp real
├── docker-compose.yml    # Orchestrarea containerelor pentru mediul local
├── .env.example          # Șablon pentru variabilele de mediu
├── .gitignore            # Fișiere și directoare ignorate de Git
└── README.md             # Documentația proiectului
```
## 4. Arhitectura de Nivel Inalt si Diagrama Fluxului

<img width="1408" height="768" alt="Arhitectura" src="https://github.com/user-attachments/assets/cefc63c9-0bc7-4868-8b76-7cb80b7038af" />


### Rolul Componentelor Principale:

1. **Discovery Server (Eureka):** Registrul central care permite microserviciilor sa se inregistreze si sa se descopere dinamic in retea.
2. **API Gateway:** Punctul unic de intrare in sistem. Valideaza securitatea si redirectioneaza cererile HTTP si conexiunile WebSocket catre serviciile interne.
3. **Auth Service:** Gestioneaza inregistrarea utilizatorilor, autentificarea si emiterea token-urilor JWT.
4. **Chat Service:** Nucleul aplicatiei. Gestioneaza sesiunile WebSocket, canalele STOMP, istoricul conversatiilor si confirmarile de livrare sau citire.
5. **PostgreSQL:** Baza de date relationala pentru stocarea persistenta a datelor de utilizator si a mesajelor.

## 5. Modelul de Date

Entitatile principale stocate in baza de date relationala sunt:

- **User**: Contine datele de identificare si credentialele utilizatorilor.
- **Conversation**: Reprezinta o conversatie individuala (1-la-1) sau un grup.
- **ConversationParticipant**: Leaga utilizatorii de conversatiile din care fac parte.
- **Message**: Contine continutul text sau metadatele mesajelor trimise.
- **MessageReceipt**: Urmareste starea de livrare si citire a fiecarui mesaj pentru fiecare participant.

## 6. Comunicare Intre Componente

- **REST / HTTP**: Utilizat pentru operatii sincrone, precum inregistrarea, autentificarea, crearea conversatiilor si preluarea istoricului de mesaje.
- **WebSocket si STOMP**: Utilizate pentru fluxul bidirectional in timp real necesar trimiterii mesajelor si actualizarii starilor.
- **Eureka**: Asigura comunicarea interna decuplata prin descoperirea automata a adreselor microserviciilor.

## 7. Testare cu Postman

Toate fluxurile sistemului pot fi verificate direct prin Postman, fara a necesita o interfata grafica:
- Testarea endpoint-urilor REST protejate prin JWT (autentificare si management conversatii).
- Testarea conexiunilor WebSocket si a transmiterii mesajelor in timp real prin protocolul STOMP / WebSocket.

## 8. Impartirea Task-urilor

* **Nistor Alexandra Meda — API Gateway, Eureka Server & Infrastructure Setup**
  * Configurarea serverului de **Eureka Service Discovery** (Registry).
  * Implementarea **Spring Cloud Gateway** (rutare cereri, CORS, rate limiting).
  * Setup-ul initial de orchestrare prin **Docker Compose** pentru serviciile de infrastructura (Eureka + Gateway).

* **Vant Catalina — Auth Service & Database Management**
  * Implementarea microserviciului **Auth Service** (Spring Boot).
  * Logica de inregistrare, autentificare, generare de token-uri JWT si gestionare a rolurilor.
  * Proiectarea schemei de date in **PostgreSQL** pentru utilizatori si integrarea ORM-ului (Spring Data JPA).

* **Vant Lorena — Chat Service & Real-Time Protocol**
  * Implementarea microserviciului **Chat Service** (Spring Boot).
  * Configurarea endpoint-urilor pentru mesagerie in timp real utilizand protocolul **WebSocket / STOMP**.
  * Logica de business pentru canale, mesaje private/grup si gestionarea sesiunilor active de chat.

* **Vlad Andreea Cristina — Chat Data Persistence, Postman Testing & Integration**
  * Proiectarea si gestionarea tabelelor **PostgreSQL** dedicate istoricului de mesaje și conversatiilor.
  * Integrarea și mentenanta containerelor de baza de date în **Docker Compose** pentru tot mediul local.
  * Crearea colectiilor complete de teste în **Postman** pentru validarea fluxului end-to-end (Auth ➔ Gateway ➔ Chat).
