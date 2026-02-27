# C# Expert Agent - Example Prompts

This document demonstrates how to effectively use the **CSharpExpert.DFDS.agent.md** agent to accelerate your C# development while maintaining DFDS engineering standards.

## Overview

The C# Expert agent helps you build production-ready .NET applications with proper security, logging, testing, and cloud-native architecture. Below are practical examples of prompts you can use.

---

## Example 1: Creating a REST API Endpoint

### Prompt
```
Create a POST endpoint in an ASP.NET Core controller for creating a new customer order. 
The endpoint should:
- Accept customer name, email, and order amount
- Validate all inputs
- Return 201 Created with the order details
- Include proper error handling and logging
```

### Expected Output
The agent will generate:
- A controller class with proper routing and HTTP attributes
- Request and response DTOs with data validation annotations
- Input validation that returns 400 Bad Request for invalid data
- Structured logging using ILogger with correlation context
- Proper HTTP status codes (201 for success, 400 for validation errors, 500 for server errors)
- Error handling that doesn't expose sensitive information
- OpenAPI/Swagger documentation attributes

The code will follow DFDS standards including:
- Secure input validation
- Structured logging with correlation IDs
- Proper async/await usage
- Repository pattern or service layer separation

---

## Example 2: Adding Input Validation and Business Rules

### Prompt
```
Add comprehensive validation to the CreateOrderRequest model:
- Customer name must be 2-100 characters
- Email must be a valid format
- Amount must be greater than 0 and less than 100,000
- Add a custom validation to ensure the email domain is from an allowed list
Also add business rule validation in the service layer to check if the customer has exceeded their order limit
```

### Expected Output
The agent will generate:
- Data annotation attributes on the DTO properties
- A custom validation attribute for email domain checking
- FluentValidation rules as an alternative approach
- Service layer business logic that queries the customer's order history
- Proper error messages that are user-friendly but don't expose system details
- Unit tests for the validation logic

The validation will be:
- Fail-fast at the API boundary with model validation
- Include business rule validation in the service layer
- Return descriptive error messages
- Log validation failures appropriately

---

## Example 3: Writing Comprehensive Unit Tests

### Prompt
```
Create unit tests for the OrderService class, specifically for the CreateOrderAsync method.
Include tests for:
- Successful order creation
- Validation failures (invalid amount, missing customer data)
- Database failure scenarios
- Business rule violations (customer order limit exceeded)
Use NUnit, Moq, and FluentAssertions
```

### Expected Output
The agent will generate:
- A test class with proper naming convention (OrderServiceTests)
- Test fixtures and setup methods for common test dependencies
- Mocked repository and dependencies using Moq
- Comprehensive test cases covering happy path and edge cases
- Async test methods using `async Task`
- FluentAssertions for readable assertions
- AAA pattern (Arrange, Act, Assert) structure
- Test data builders or factories for creating test objects

Tests will include:
- Verification that the repository was called correctly
- Assertions on the returned data
- Exception handling tests
- Logging verification to ensure proper observability

---

## Example 4: Optimizing Performance for High-Throughput Scenarios

### Prompt
```
Optimize the GetOrdersForCustomer method which currently has N+1 query problems.
The method fetches a list of orders and then loads related customer and product data.
Improve performance using:
- Eager loading or projections
- Caching for frequently accessed data
- Async operations
- Pagination support
Include performance metrics logging
```

### Expected Output
The agent will generate:
- Entity Framework Core queries with `.Include()` for eager loading
- Or alternative: projections using `.Select()` to fetch only needed data
- Caching implementation using IMemoryCache or IDistributedCache
- Pagination parameters (page number, page size) with sensible defaults
- Async/await throughout the data access layer
- Performance logging that tracks query execution time
- Response headers for pagination metadata (total count, page info)

The optimized code will:
- Eliminate N+1 queries by loading related data in a single query
- Cache customer and product lookups that rarely change
- Support pagination to limit data transfer
- Log slow queries for monitoring
- Return only necessary data to reduce payload size

---

## Example 5: Implementing Retry Logic with Circuit Breaker

### Prompt
```
Implement a service that calls an external payment API with resilience patterns.
Use Polly to add:
- Retry policy with exponential backoff (3 retries)
- Circuit breaker that opens after 5 consecutive failures
- Timeout policy of 10 seconds
- Logging for each retry attempt and circuit breaker state changes
The service should be dependency-injected and testable
```

### Expected Output
The agent will generate:
- Service registration in Startup.cs or Program.cs using `AddHttpClient`
- Polly policies configured with exponential backoff strategy
- Circuit breaker configuration with failure threshold and break duration
- Timeout policy wrapping the HTTP calls
- Structured logging for retry attempts, circuit breaker trips, and failures
- Interface and implementation for dependency injection
- Unit tests that verify retry behavior using mocked HttpMessageHandler

The implementation will:
- Gracefully handle transient failures with retries
- Prevent overwhelming a failing service with the circuit breaker
- Protect against hung requests with timeouts
- Provide full observability of resilience patterns in action
- Be fully testable with dependency injection

---

## Example 6: Consuming and Publishing Kafka Messages with Dafda

### Prompt
```
Create a Kafka consumer for an "order-placed" event using Dafda. The handler should:
- Persist the order to a SQL database
- Publish an "order-confirmed" event back to Kafka using the outbox pattern
- Include structured logging and error handling
```

### Expected Output
The agent will generate:
- `AddConsumer` registration in `Program.cs` with `WithBootstrapServers`, `WithGroupId`, and `RegisterMessageHandler`
- An `IMessageHandler<OrderPlaced>` implementation with injected dependencies
- `AddOutbox` and `AddProducer` registration for reliable outbound publishing
- An `IOutbox`-based publish call inside the service layer
- Structured logging using `ILogger<T>` at key handler steps
- Idempotent handler logic (safe to re-process duplicate deliveries)

The code will follow DFDS Dafda standards including:
- Dafda consumer/producer registration via DI extension methods
- Outbox pattern for at-least-once, durable message publishing
- No direct use of the raw `Confluent.Kafka` client
- Message handler registered and resolved through the DI container

---

## Tips for Using the Agent Effectively

### Be Specific About Requirements
Instead of "create a service," specify:
- What the service does
- What patterns to use (repository, CQRS, etc.)
- What external dependencies it has
- What standards to follow (DFDS-specific requirements)

### Request Tests Alongside Code
Ask for unit tests in the same prompt to ensure testability:
```
Create an OrderService with dependency injection and include comprehensive unit tests
```

### Ask for Explanations
Request comments or explanations for complex logic:
```
Add a caching strategy with explanation of cache key generation and invalidation logic
```

### Iterate and Refine
Use follow-up prompts to refine generated code:
```
Refactor the previous controller to use minimal APIs instead of MVC controllers
```

### Specify Frameworks and Versions
Be explicit about technology choices:
```
Create a .NET 8 minimal API using Entity Framework Core 8 with PostgreSQL
```

---

## What the Agent Won't Do

The agent follows DFDS standards and won't:
- Generate code with hardcoded secrets or credentials
- Skip input validation or error handling
- Create code without logging
- Ignore security best practices
- Use deprecated or outdated patterns

If you ask for something that violates standards, the agent will suggest the secure/proper alternative.

---

## Next Steps

1. **Copy the agent files** to your project's `.github/agents/` folder:
   ```bash
   # Copy base agent (required)
   cp path/to/dfds-copilot-agents/agents/DFDS.agent.md .github/agents/
   
   # Copy C# expert agent (automatically extends base agent)
   cp path/to/dfds-copilot-agents/agents/CSharpExpert.DFDS.agent.md .github/agents/
   ```
2. **Try these prompts** in your IDE with GitHub Copilot enabled
3. **Customize prompts** based on your specific project needs
4. **Share feedback** to help improve the agent definitions

Happy coding! 🚀
