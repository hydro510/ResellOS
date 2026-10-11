# ResellOS — Architecture Notes

## 1. Architecture Overview

ResellOS will be a full-stack web application consisting of a frontend, backend, and database.

These components will work together to allow users to interact with the application, process business operations, and store information.

The exact technologies and frameworks have not yet been selected.

## 2. Frontend

The frontend is the client-side portion of the application that users interact with through their web browsers.

### Responsibilities

- Display products, inventory, sales, and financial information.
- Provide forms for entering and editing business data.
- Handle user interactions.
- Perform basic input validation to improve the user experience.
- Communicate with the backend through API requests.
- Display success messages and errors returned by the backend.

### Potential Technologies

- HTML
- CSS
- JavaScript
- A frontend framework, if justified by the application's requirements.

**Technology decision:** Undecided.

## 3. Backend

The backend is the server-side portion of the application responsible for processing requests, enforcing business rules, and communicating with the database.

### Responsibilities

- Receive and process requests from the frontend.
- Validate incoming data.
- Handle business operations such as recording purchases and sales.
- Calculate revenue, costs, and profits.
- Enforce authentication and authorization.
- Prevent invalid inventory operations.
- Read and write information through the database.
- Return appropriate responses and errors to the frontend.

### Example

If a reseller attempts to sell more products than are available in inventory, the backend should reject the transaction rather than allowing inventory quantities to become inconsistent.

**Technology decision:** Undecided.

## 4. Database

The database will provide persistent storage for the application's business information.

### Responsibilities

- Store user and business account information.
- Store product and inventory records.
- Store purchase and sales transactions.
- Store recorded expenses.
- Maintain relationships between business records.
- Support consistent and reliable data operations.

The backend will communicate with the database to retrieve, create, update, and manage records.

A relational database is a candidate because the application's information has relationships, such as products belonging to businesses and sales referencing products.

**Technology decision:** Undecided.

## 5. API Communication

The frontend and backend will communicate through an application programming interface (API).

For example, when a user requests their inventory, the frontend will send a request to the backend. The backend will retrieve the appropriate records from the database and return a response.

REST over HTTP is a potential approach, but the API design has not yet been finalized.

## 6. Example Data Flow — Recording a Sale

1. A reseller opens the sales form in the frontend.
2. The reseller selects a product and enters the quantity and selling price.
3. The frontend sends the sale information to the backend.
4. The backend validates the information and checks the available inventory.
5. If the sale is valid, the backend records the transaction and updates inventory through the database.
6. The database changes must succeed together to prevent inconsistent records.
7. The backend returns a success response.
8. The frontend displays the updated information.

If the sale is invalid, the backend returns an appropriate error without recording the sale.

## 7. Security Considerations

ResellOS will support multiple independent resellers.

The application must ensure that:

- Users authenticate before accessing protected business information.
- Users can only access records they are authorized to view or modify.
- Sensitive information is handled securely.
- Backend validation cannot be bypassed by manipulating frontend requests.
- Database operations maintain consistent records.

Specific authentication and authorization approaches will be evaluated later.

## 8. Initial Engineering Decisions

| Category | Current Decision |
|---|---|
| Repository visibility | Public |
| Repository setup | GitHub repository cloned locally |
| Frontend technologies | Undecided |
| Backend technologies | Undecided |
| Database | Undecided |
| API architecture | Undecided |
| Authentication approach | Undecided |
| Hosting and deployment | Undecided |

## 9. Open Architecture Questions

The following questions will be investigated during development:

1. Which frontend approach best fits the project?
2. Which backend language and framework should be used?
3. Which database is most appropriate?
4. How should products, purchases, sales, and expenses be related?
5. How will user authentication and business data isolation work?
6. How can inventory remain accurate when multiple operations happen simultaneously?
7. How should the application handle server or database failures?
8. How should ResellOS be tested and deployed?


## 10. Request-Response LifeCycle

User Interaction: When a reseller opens the inventory dashboard, they see all of their products, current quantities, units sold, and other potential metrics. When a user navigates to a product of their choosing, they can see how much stock is available.

Frontend Request: JavaScript sends an HTTP request (e.g., GET /api/products or GET /api/inventory) to the backend to retrieve inventory data and check product availability.

Backend Processing & Authentication Check:

The backend validates the user's authenticated credentials, such as a session cookie or an authentication token. 

If the user is not authenticated: The backend immediately halts processing and returns a 401 Unauthorized status code with an error message directing the user to log in.

Authorization & Database Interaction:

The backend verifies user permissions.

If the user attempts to access another reseller's products: The backend blocks the request.

If authorized: The backend queries the database, scoping records strictly to the authenticated business. It handles safe transaction execution, concurrent reads/writes, and rollback protocols if conflicts occur.

If the database is temporarily unavailable: The backend catches the connection timeout or failure, logs the incident, and returns a 503 Service Unavailable (or 500 Internal Server Error) status code.

HTTP Response (Success): When the database query succeeds, the backend returns the correct inventory data in a structured JSON format with a 200 OK status code.

Frontend Update: The browser parses the JSON response, displays the available products, and updates the UI based on live stock counts. In the event of an error response (401, 403, or 503), the appropriate user-friendly error message or login prompt is rendered on screen.