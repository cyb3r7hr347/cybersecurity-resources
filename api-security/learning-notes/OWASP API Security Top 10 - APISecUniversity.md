# OWASP API Security Top 10

## 1. Broken Object Level Authorization (BOLA)

Broken Object Level Authorization allows attackers to bypass authorization and access resources they shouldn't have permission to access. This vulnerability occurs when applications fail to properly validate that the requesting user has permission to access the requested object.

### Risk Impact
- Unauthorized access to sensitive data
- Data theft or manipulation
- Privacy violations
- Regulatory compliance issues

### Attack Scenario
Consider an e-commerce API endpoint:
```
GET /api/users/12345/orders/789
```
An attacker with valid credentials for user ID 54321 modifies the request to:
```
GET /api/users/54321/orders/789
```
If the API only validates authentication but not authorization, the attacker gains access to another user's order details.

### Prevention
1. Implement proper authorization checks at the object level
2. Create and enforce data access policies
3. Use random, unpredictable object IDs
4. Log access attempts and implement monitoring

## 2. Broken Authentication

Broken Authentication encompasses implementation flaws in authentication mechanisms that allow attackers to impersonate legitimate users or bypass authentication entirely.

### Risk Impact
- Account takeover
- Identity theft
- Data breaches
- Unauthorized access to systems

### Attack Scenarios
1. Credential Stuffing:
```
POST /api/auth/login
{
    "username": "[compromised email]",
    "password": "[compromised password]"
}
```

2. Weak Token Implementation:
```
Authorization: Bearer abc123.xyz789
```
Where the token lacks proper entropy or validation

### Prevention
1. Implement strong password policies
2. Use secure session management
3. Require strong authentication credentials
4. Implement rate limiting and account lockout
5. Use secure token generation and validation

## 3. Broken Object Property Level Authorization

This vulnerability occurs when an API exposes endpoints that handle object properties without proper authorization checks, allowing manipulation of properties that should be restricted.

### Risk Impact
- Privilege escalation
- Data manipulation
- Unauthorized access to sensitive properties

### Attack Scenario
Original user profile update:
```json
PATCH /api/v1/users/profile
{
    "name": "John Doe",
    "email": "john@example.com"
}
```

Malicious attempt to elevate privileges:
```json
PATCH /api/v1/users/profile
{
    "name": "John Doe",
    "role": "admin",
    "isVerified": true
}
```

### Prevention
1. Implement property-level access controls
2. Explicitly define modifiable properties
3. Validate input against allowed properties
4. Implement role-based property access

## 4. Unrestricted Resource Consumption

This vulnerability occurs when APIs don't properly restrict the size or number of resources that can be requested by the client, leading to denial of service.

### Risk Impact
- Service degradation
- Increased operational costs
- System unavailability
- Resource exhaustion



### Prevention
1. Implement rate limiting
2. Enforce pagination
3. Limit payload sizes
4. Set timeouts for long-running operations
5. Monitor and alert on abnormal consumption patterns

## 5. Broken Function Level Authorization (BFLA)

BFLA occurs when API endpoints fail to properly enforce restrictions on functionality that should be limited to specific user roles.

### Risk Impact
- Unauthorized administrative access
- Business logic bypass
- Data manipulation through unauthorized functions

### Attack Scenario
Regular user attempting to access admin functionality:
```
POST /api/admin/users
Content-Type: application/json
Authorization: Bearer [regular-user-token]

{
    "username": "malicious_admin",
    "role": "admin"
}
```

### Prevention
1. Implement role-based access control
2. Validate user permissions for each function
3. Use proper authorization checks at all endpoints
4. Maintain clear separation between regular and admin functions

## 6. Unrestricted Access to Sensitive Business Flows

This vulnerability involves the abuse of legitimate business workflows through excessive automated use or manipulation.

### Risk Impact
- Business logic abuse
- Financial losses
- Service disruption
- Competitive disadvantage

### Attack Scenario
Automated ticket purchasing:
```python
for event in events:
    POST /api/tickets/purchase
    {
        "event_id": event.id,
        "quantity": max_allowed
    }
```

### Prevention
1. Implement business-specific rate limits
2. Add friction to sensitive operations
3. Monitor for unusual patterns
4. Implement advanced bot detection

## 7. Server Side Request Forgery (SSRF)

SSRF allows attackers to make the API server perform unintended requests to internal or external systems.

### Risk Impact
- Access to internal systems
- Data exfiltration
- Remote code execution
- Service compromise

### Attack Scenario
Malicious request attempting to access internal systems:
```
POST /api/fetch-data
{
    "url": "http://internal-network/sensitive-data"
}
```

### Prevention
1. Validate and sanitize input URLs
2. Implement allowlists for external services
3. Use proper network segmentation
4. Block requests to internal networks

## 8. Security Misconfiguration

Security misconfiguration encompasses various configuration weaknesses that can compromise API security.

### Risk Impact
- System compromise
- Data leakage
- Unauthorized access
- Service disruption

### Attack Scenarios
1. Exposed error messages:
```json
{
    "error": "Database connection failed",
    "details": "MySQL connection to 192.168.1.1:3306 failed",
    "stack": "[detailed stack trace]"
}
```

2. Default credentials remaining active:
```
GET /api/admin
Authorization: Basic admin:admin
```

### Prevention
1. Implement secure configuration practices
2. Regular security audits
3. Use security headers
4. Remove unnecessary features and documentation
5. Keep systems and dependencies updated

## 9. Improper Inventory Management

This vulnerability arises from poor API versioning, documentation, and retirement practices.

### Risk Impact
- Exposure through legacy endpoints
- Security bypass through older versions
- Unauthorized access through unknown endpoints

### Attack Scenario
Accessing deprecated but functional endpoint:
```
GET /api/v1/users/export
```
When current version is:
```
GET /api/v2/users/export-with-auth
```

### Prevention
1. Maintain API inventory
2. Document all endpoints and versions
3. Properly retire old versions
4. Monitor API usage
5. Implement API gateway controls

## 10. Unsafe Consumption of APIs

This vulnerability occurs when an application consumes third-party APIs without proper security validation.

### Risk Impact
- Supply chain attacks
- Data manipulation
- System compromise through trusted channels

### Attack Scenario
Blind trust in third-party API response:
```python
response = requests.get('https://third-party-api/data')
process_data(response.json())  # No validation performed
```

### Prevention
1. Validate all third-party API responses
2. Implement proper error handling
3. Use API security testing
4. Monitor third-party API behavior
5. Maintain security requirements for API providers