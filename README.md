# ProductOwner_AI

### Product Owner (PO) Agent

```
Ingest & Parse  →  RAG Retrieval  →  Story Drafting  →  Quality Check
                                                            │
                                              passes? ──────┼────── fails?
                                                │           │          │
                                          Human Review    Refine  ─── ↩ (loop back)
                                                │
                                             OUTPUT
```
### Test Case Agent 

```
Receive Story  →  Parse Code Context  →  ┌─ Gherkin Generator ─┐  →  Validate  →  OUTPUT
                  (AST extraction)       └─ JUnit Generator  ──┘
```

**Output examples:**

## JSON

```json
{
  "story_id": "PO-42",
  "title": "As a user, I want to ...",
  "description": "Detailed description...",
  "acceptance_criteria": [
    "GIVEN ... WHEN ... THEN ...",
    "GIVEN ... WHEN ... THEN ..."
  ],
  "priority": "High",
  "story_points": 5,
  "labels": ["payments", "backend"],
  "edge_cases": ["..."],
  "dependencies": ["PO-41"]
}
```
## Gherkins

```gherkin
Feature: User Login
  As a registered user
  I want to log in with my credentials
  So that I can access my dashboard

  Scenario: Successful login with valid credentials
    Given the user is on the login page
    And the user has a valid account
    When the user enters valid email and password
    And clicks the "Login" button
    Then the user should be redirected to the dashboard
    And a welcome message should be displayed

  Scenario: Failed login with invalid password
    Given the user is on the login page
    When the user enters a valid email and incorrect password
    And clicks the "Login" button
    Then an error message "Invalid credentials" should be displayed
    And the user should remain on the login page
```
## JUnit

```java
import org.junit.jupiter.api.*;
import static org.junit.jupiter.api.Assertions.*;

class UserLoginTest {

    @Test
    @DisplayName("Successful login with valid credentials")
    void testSuccessfulLogin() {
        // Arrange
        LoginService loginService = new LoginService();
        String email = "user@example.com";
        String password = "validPass123";

        // Act
        AuthResult result = loginService.login(email, password);

        // Assert
        assertTrue(result.isSuccess());
        assertNotNull(result.getToken());
        assertEquals("user@example.com", result.getUserEmail());
    }

    @Test
    @DisplayName("Failed login with invalid password")
    void testFailedLoginInvalidPassword() {
        // Arrange
        LoginService loginService = new LoginService();

        // Act
        AuthResult result = loginService.login("user@example.com", "wrongPass");

        // Assert
        assertFalse(result.isSuccess());
        assertEquals("Invalid credentials", result.getErrorMessage());
    }
}
```

## Basic Project Structure

```
agentic-sdlc/
├── backend/
│   ├── app.py                  # FastAPI entry point + all routes
│   ├── agents.py               # PO Agent + Test Case Agent (LangGraph)
│   ├── rag.py                  # Document loading, chunking, retrieval
│   ├── parsers.py              # Python & Java code parsers
│   ├── jira_client.py          # Jira API integration
│   ├── models.py               # Pydantic schemas (Story, TestCase)
│   ├── prompts.py              # All LLM prompt templates
│   ├── config.py               # Settings via pydantic-settings
│   ├── requirements.txt
│   └── .env
│
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── app.js                  # Single-page app
│
├── uploads/                    # Uploaded documents land here
├── outputs/                    # Generated .feature & .java files
├── chroma_data/                # ChromaDB persistence
├── .env.example
└── README.md

```
## Agentic SDLC Platform

Layer 1 — Presentation	
React UI components (upload, story reviewer, test viewer, Jira import)

Layer 2 — API	
FastAPI REST + WebSocket streaming + Auth

Layer 3 — Agent Orchestration	
LangGraph with PO Agent (ingest → RAG → draft → quality loop) and Test Case Agent (story → code context → Gherkin/JUnit → validate)

Layer 4 — Tools & Services	
RAG pipeline, Python/Java parsers, Jira client, code analyzer

Layer 5 — Data & Infrastructure	
ChromaDB, SQLite/PostgreSQL, file storage, OpenAI API

## Final Deliverable

A working system that:
Accepts documents + Jira + Code
Runs RAG
Orchestrates agents via LangGraph
Produces
 - Refined stories
 - Acceptance criteria
 - Functional test Scenarios
 - Gherkins BDD tests
 - Technical tests
 - JUnit skeletons
Stores output in structured formats
