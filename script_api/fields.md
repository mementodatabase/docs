---
title: Field Types
parent: Memento JavaScript API
nav_order: 11
layout: default
---

# Field Types
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

- TOC
{:toc}

# Field Types and Return Values

When using `entry().field()` or `entry().set()`, the following types are used:

| Field Type | Return/Parameter Type |
|------------|---------------------|
| Text | string |
| Number | number |
| Date | Date |
| Checkbox | boolean |
| Multiple-choice | string[] |
| Link to Entry | Entry[] |
| Link to File | string[] |
| Image | JSImage[] |
| Contact | JSContact |
| Location | JSGeolocation |
| Calculation | string or number |
| Embedded | object[] |

# JSContact Object

Represents a contact stored in a Contact field. Provides access to contact information and communication methods.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| fullName | string | The contact's full name |
| phone | string | Primary phone number |
| email | string | Primary email address |
| hasNext | boolean | Indicates if there are more contacts in the field |
| next | JSContact | Reference to the next contact in multi-contact fields |

## call()

Initiates a phone call to the contact's primary phone number.

#### Returns
{: .no_toc }
void

#### Example
{: .no_toc }
```javascript
var contact = entry().field("businessContact");
if (contact.phone) {
    contact.call();
    log("Calling " + contact.fullName);
}
```

## sendEmail(subject, message)

Sends an email to the contact's primary email address.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|-----------|------|-------------|
| subject | string | Email subject line |
| message | string | Email body content |

#### Example
{: .no_toc }
```javascript
var contact = entry().field("customerContact");
contact.sendEmail("Order Confirmation", "Your order has been processed.");
log("Email sent to " + contact.email);
```

## sendSMS(message)

Sends an SMS text message to the contact's primary phone number.

#### Parameters
{: .no_toc }

| Parameter | Type | Description |
|-----------|------|-------------|
| message | string | Text message content |

#### Example
{: .no_toc }
```javascript
var contact = entry().field("teamMember");
contact.sendSMS("Meeting reminder for tomorrow");
log("SMS sent to " + contact.phone);
```

## show()

Opens the device's contact app displaying this contact's details.

#### Example
{: .no_toc }
```javascript
var contact = entry().field("clientContact");
contact.show();
log("Showing contact details for " + contact.fullName);
```

# JSGeolocation Object

Represents a geographical location stored in a Location field. Provides access to coordinates and address information.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| address | string | Formatted address of the location |
| lat | number | Latitude coordinate |
| lng | number | Longitude coordinate |
| hasNext | boolean | Indicates if there are more locations in the field |
| next | JSGeolocation | Reference to the next location in multi-location fields |

#### Example
{: .no_toc }
```javascript
var e = entry();
var location = e.field("myLocation");

// Extract coordinates to separate fields
e.set("LocationLatitude", location.lat);
e.set("LocationLongitude", location.lng);

log("Location coordinates: " + location.lat + ", " + location.lng);
log("Address: " + location.address);
```

# JSImage Object

Represents an image stored in an Image field.

## Properties

| Property | Type | Description |
|----------|------|-------------|
| uri | string | URI path to the image file |
| caption | string | Image caption (readable and writable) |
| index | number | Position index in multi-image fields |

## view()

Displays the image in the device's image viewer.

#### Example
{: .no_toc }
```javascript
var images = entry().field("productPhotos");
images.forEach(function(img) {
    log("Image caption: " + img.caption);
    log("Image URI: " + img.uri);
    img.view();
});
```
