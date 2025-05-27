---
title: Email
parent: Memento JavaScript API
nav_order: 7
layout: default
---

# Email
{: .no_toc } 

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

The Email API provides functionality for sending email messages through SMTP servers in Memento Database scripts.

# Global Functions

## email()
Returns the system Email object that provides email-related functionality.

#### Returns
{: .no_toc } 
- `Email` - The system Email object

#### Example
{: .no_toc } 
```javascript
let emailObj = email();
log("Email object created");
```

# Email Object

The Email object provides methods for sending email messages through configured SMTP servers.

## send(cfg, to, subject, message)
Sends an email message using the specified SMTP configuration.

#### Parameters
{: .no_toc } 

| Parameter | Type | Description |
|-----------|------|-------------|
| cfg | EmailConfig | SMTP configuration object containing server details |
| to | String | Recipient email address |
| subject | String | Email subject line |
| message | String | Email body content |

#### Returns
{: .no_toc } 
- `void`

#### Example
{: .no_toc } 
```javascript
// Create SMTP configuration
var cfg = {
    "host": "smtp.example.com",
    "port": 25,
    "user": "username",
    "pass": "password",
    "from": "username@example.com"
};

// Send email
email().send(cfg, "to@email.com", "Test Subject", "Hello from Memento!");
log("Email sent successfully");
```
