# 📝 Black Diamond Wealth Platform – API & Microservice Breakdown

## 📌 Overview
The **SS&C Black Diamond Wealth Platform** provides a comprehensive suite of tools for portfolio management, reporting, billing, compliance, CRM integration, and more.  
To extend functionality, Black Diamond exposes a set of **developer APIs** that enable programmatic access to financial data, batch exports, and integrations with external systems.  

This document captures:
- Authentication details
- Available endpoints
- Batch API (V5) workflow
- Suggested microservice breakdown
- Next steps for implementation

---

## 🔑 API Access & Authentication

Black Diamond APIs are accessible through the **Developer Portal**.  
Every API request requires two key components:  

1. **OAuth 2.0 Token**  
2. **Subscription Key**  

### Authentication Flow
- **Endpoint:**  
  ```http
  POST https://login.bdreporting.com/connect/token
  ```

- **Password Grant Payload Example:**
  ```x-www-form-urlencoded
  grant_type=password
  client_id=your_client_id
  client_secret=your_client_secret
  username=your_username
  password=your_password
  ```

- **Response:**
  ```json
  {
    "access_token": "<JWT Access Token>",
    "expires_in": 600,
    "refresh_token": "<Refresh Token>",
    "token_type": "Bearer"
  }
  ```

- **Refresh Token Flow:**  
  ```x-www-form-urlencoded
  grant_type=refresh_token
  client_id=your_client_id
  client_secret=your_client_secret
  refresh_token=<Refresh Token>
  ```

- **Headers required for all requests:**
  ```
  Authorization: Bearer <AccessToken>
  Ocp-Apim-Subscription-Key: <Your Subscription Key>
  ```

---

## 📦 Batch API (V5)

The **Batch API V5** is the primary method for exporting large datasets from Black Diamond.  
It returns a **ZIP file containing multiple JSON files** (e.g., Accounts, Portfolios, Assets, Clients, etc.).

### Endpoint
```http
POST https://api.blackdiamondwealthplatform.com/batchV5
```

### Example Request
```json
{
  "ReturnDate": "2025-09-01",
  "batchFiles": ["Accounts", "Portfolios", "Relationships", "Assets"],
  "SendToFTP": true,
  "FtpConfiguration": {
    "Server": "sftp.mycompany.com",
    "Username": "sftp_user",
    "Password": "sftp_password",
    "RemoteFolder": "/blackdiamond/data"
  },
  "CallbackUrl": "https://mycompany.com/api/batch/callback"
}
```

### Workflow
1. Authenticate to retrieve **access token**.  
2. Submit batch request → receive **Batch ID**.  
3. Poll status until batch is ready.  
4. Retrieve results (ZIP with JSON files).  
5. Optionally use:
   - **Callback URL** (for async notification).  
   - **SFTP Delivery** (automated transfer).  

---

## 🏗 Suggested Microservice Breakdown

To integrate Black Diamond APIs into our environment, we can design modular microservices:

| Microservice            | Responsibilities |
|-------------------------|------------------|
| **Auth Service**         | Manages OAuth flow, token refresh, and secure storage of tokens. |
| **Batch Data Service**   | Handles batch job creation, polling, and retrieval of results. |
| **Data Processor**       | Unzips batch files, parses JSON, and normalizes data into internal schema. |
| **Integration Service**  | Syncs normalized data with CRMs, reporting dashboards, and planning tools. |
| **Notification Service** | Listens for callback events, triggers alerts or downstream jobs. |
| **Monitoring Service**   | Logs API health, errors, throughput, and alerts for SLA breaches. |
| **API Gateway**          | Exposes a unified REST/GraphQL interface for internal apps and controls routing. |

---

## 🔗 Integrations & Ecosystem
Black Diamond APIs are designed to integrate with:
- **CRM Systems:** Salesforce, Redtail, Salentica  
- **Financial Planning:** MoneyGuidePro, eMoney  
- **UMA / Trading Platforms:** SMArtX  
- **Custodians:** Direct connections for portfolio reconciliation  

This ensures **data consistency** across multiple platforms.

---

## ✅ Next Steps

- [ ] **Register** on the [Black Diamond Developer Portal](https://www.blackdiamondwealthplatform.com/) to obtain subscription keys.  
- [ ] **Implement OAuth Service** to handle token lifecycle.  
- [ ] **Build POC** using the **Batch API V5** to pull Accounts + Portfolios.  
- [ ] **Map JSON Schema** (Accounts, Portfolios, Relationships, Assets) to our internal data model.  
- [ ] **Design Microservices** (Auth, Batch Data, Processor, Integrator, Notification).  
- [ ] **Document Error Handling & Retry Logic** for token expiration and failed batch jobs.  
- [ ] **Evaluate Integrations** (CRM, dashboards, compliance systems).  

---

## 📚 References
- [SS&C Black Diamond Wealth Platform](https://www.sscblackdiamond.com/)  
- [Black Diamond Developer Portal](https://www.blackdiamondwealthplatform.com/)  
- [Batch API V5 Documentation](https://www.blackdiamondwealthplatform.com/batch_API-V5)  
- [OAuth Authentication Guide](https://www.blackdiamondwealthplatform.com/authentication_password_flow)  

---

✍️ *This document is intended for internal technical review and serves as a foundation for designing our integration strategy with Black Diamond APIs.*
