# Azure-Logic-Apps

Azure Logic Apps and Azure Functions are powerful tools for creating workflows and serverless applications. Here's an overview and sample code for both:

---

## **Azure Logic Apps**
Azure Logic Apps is a cloud-based platform for automating workflows and integrating apps, data, and services.

### **Use Cases**
- Automating workflows (e.g., file processing, approvals).
- Integration with SaaS applications (e.g., Office 365, Salesforce).
- Data transformation and ETL processes.

### **Creating a Logic App**
1. Go to the Azure Portal.
2. Create a new Logic App.
3. Use the designer to build workflows with pre-built connectors and triggers.

### **Logic App JSON Code Example**
Below is an example of a Logic App JSON template that triggers when an HTTP request is received and sends an email using Office 365.

```json
{
  "definition": {
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "actions": {
      "Send_an_email": {
        "inputs": {
          "body": {
            "To": "recipient@example.com",
            "Subject": "Hello from Logic Apps",
            "Body": "This is a test email sent by a Logic App."
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
            }
          },
          "method": "post",
          "path": "/v2/Mail/Send"
        },
        "runAfter": {},
        "type": "ApiConnection"
      }
    },
    "triggers": {
      "manual": {
        "inputs": {},
        "metadata": {},
        "type": "Request"
      }
    }
  },
  "parameters": {
    "$connections": {
      "defaultValue": {},
      "type": "Object"
    }
  }
}
```

### **Deploy Logic App**
Deploy using the Azure CLI:

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file logicapp.json
```

---

## **Azure Functions**
Azure Functions is a serverless compute service that lets you run code on demand.

### **Use Cases**
- Event-driven applications.
- Data processing and transformation.
- Backend services for APIs and mobile apps.

### **Function App Code Examples**
Azure Functions support multiple languages like C#, Python, JavaScript, etc.

#### **HTTP Trigger (JavaScript)**
```javascript
module.exports = async function (context, req) {
    context.log('HTTP trigger function processed a request.');

    const name = req.query.name || (req.body && req.body.name);
    if (name) {
        context.res = {
            body: `Hello, ${name}!`
        };
    } else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```

#### **Blob Trigger (Python)**
```python
import logging

def main(myblob: bytes):
    logging.info(f"Blob trigger function processed blob of size: {len(myblob)} bytes")
```

### **Creating and Deploying Azure Functions**
1. **Create a Function App**:
   - In Azure Portal, create a Function App.
   - Choose your runtime stack (e.g., Node.js, Python, etc.).

2. **Develop Locally**:
   Use Azure Functions Core Tools:
   ```bash
   func init myFunctionApp --javascript
   cd myFunctionApp
   func new --template "HTTP trigger" --name HttpTrigger
   ```

3. **Deploy to Azure**:
   ```bash
   func azure functionapp publish <function-app-name>
   ```

---

## **Logic Apps + Azure Functions Integration**
Azure Logic Apps can call Azure Functions to extend workflows.

### **Example Workflow**
1. Logic App Trigger: HTTP Request.
2. Action: Call Azure Function to process data.
3. Action: Store processed data in a database.

#### **Logic App Integration with Azure Function**
```json
{
  "definition": {
    "triggers": {
      "manual": {
        "inputs": {},
        "metadata": {},
        "type": "Request"
      }
    },
    "actions": {
      "Call_Function": {
        "inputs": {
          "body": {
            "name": "@triggerBody()?['name']"
          },
          "method": "post",
          "uri": "https://<your-function-app>.azurewebsites.net/api/HttpTrigger"
        },
        "runAfter": {},
        "type": "Http"
      }
    }
  }
}
```

---

## **Best Practices**
### Logic Apps
- Use **Connectors** to simplify integrations.
- Leverage **Error Handling**: Add retries and run-after conditions.
- Split complex workflows into **Child Logic Apps**.

### Azure Functions
- Optimize cold start performance using **Premium Plan** or **Always On**.
- Use **App Insights** for monitoring.
- Secure HTTP-triggered functions with **API Keys** or **Azure AD**.

Let me know if you need deeper examples or more detailed walkthroughs!
