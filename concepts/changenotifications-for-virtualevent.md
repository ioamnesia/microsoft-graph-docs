---
title: "Get change notifications for Microsoft Teams virtual event updates"
description: "Use change notifications in Microsoft Graph to enable you to subscribe to various events for Microsoft Teams online meetings."
author: "benlee-msft"
ms.localizationpriority: high
ms.prod: "cloud-communications"
ms.custom: scenarios:getting-started
---

# Get change notifications for Microsoft Teams virtual event updates

## Subscribe to notifications for virtual event updates.

To get change notifications for virtual events, you may specify the resource as the following: 

### Subscribeable resources
- `solutions/virtualEvents/events` to receive notifications for all virtual events in tenant. This type of subscription is limited to created updates for events.
- `solutions/virtualEvents/events/{eventId}` to receive notifications for a specific virtual event.

***Please use the ?$filter odata query parameter to specify the event organizer and co-organizers that are interested in notifications. Only notifications with matching event id and contain the organizer/coorganizer id will receive messages. Examples for Odata query params are listed below: ---> Are distingu***
 - ***`solutions/virtualEvents/events/?$filter=organizerid eq '{organizer}' and coorganizerids in ('{co-organizer1}', '{co-organizer2}')` 
 - ***`solutions/virtualEvents/events/{eventId}?$filter=organizerid eq '{organizer}' and coorganizerids in ({co-organizer1}', '{co-organizer2}')` 
 - Will need clarity on how to represent these as it can be confusing. Are these organizers and co-organizers combinations specific? ie.) notifications must have these organizers and coorganizers. 

**Note:** Please replace values with paranthesis with actual values.

### Permissions

| Permission type                       | Permissions (from least to most privileged)              | Supported versions |
|:--------------------------------------|:---------------------------------------------------------|:-------------------|
| Delegated (work or school account)    | Not supported.                                           | Not supported.     |
| Delegated (personal Microsoft account)| Not supported.                                           | Not supported.     |
| Application                           | VirutalEvent.Read.All                                    | beta               |

### Subscription to all created events in a tenant
```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "created",
  "notificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

For delegated tokens, notifications for events will only be delievered to a subscriber if the user token's object id matches an organizer or co-organizer id on notifications.

### Subscription to specific event's updated and deleted
```HTTP
POST https://graph.microsoft.com/beta/subscriptions
Content-Type: application/json

{
  "changeType": "updated",
  "notificationUrl": "https://webhook.contoso.com/api",
  "resource": "solutions/virtualEvents/events/{eventId} ",
  "expirationDateTime": "2021-02-01T11:00:00.0000000Z",
  "clientState": "secretClientState"
}
```

For delegated tokens, notifications for specific event id will only be delievered to a subscriber if the user token's object id matches an organizer or co-organizer id on notifications.

## Receiving event notifications

Virtual events resources receives notifications for the following changes:
- **Event:** Refers to virtual event changes.
- **Session:** Refers to the online meeting sessions created for virtual event.
- **Registrant:** Refers to participant registration at the event level. While these notifications are at event level, the meta data can contain references to sessions.

Notifications will include the resource url of the changed resource. A separate request to graph will be required to obtain the information of the changed resource.

The below table indicates the supported notification and change types for the virtual events resource.

### Notification types

| Notification type      | Resource id                                                                                 | Change types    |
|:-----------------------|:--------------------------------------------------------------------------------------------|:----------------|
| [Event]()              | solutions/virtualEvents/events/{eventId}                                                    | *created*, updated, deleted  |
| [Session]()            | solutions/virtualEvents/events/{eventId}/sessions/{sessionId}                         | created, updated, *deleted*  |
| [Registrant]()         | solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}              | created, *updated*, *deleted*  |

## Event notification examples
### Event created --> Listed as Postbuild in the docs.
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "created",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/"
    }
  }]
}
```

### Event updated

```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "updated",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/"
    }
  }]
}
```

### Event deleted

```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "deleted",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/"
    }
  }]
}
```

## Session notification examples
### Session created
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "created",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}"
    }
  }]
}
```

### Session updated
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "updated",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}"
    }
  }]
}
```

### Session deleted
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "deleted",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/sessions/{sessionId}"
    }
  }]
}
```

## Registrant notifications examples
### Registrant created
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "created",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}"
    }
  }]
}
```

### Registrant deleted
```HTTP
{
  "value": [{
    "subscriptionId": "7015b436-a8b8-4260-af80-5af8cba32e62",
    "clientState": "secret client state",
    "changeType": "deleted",
    "tenantId": "f5b076c8-b508-4ba3-a1a7-19d1c0bcef03",
    "resource": "solutions/virtualEvents/events/{eventId}/",
    "subscriptionExpirationDateTime": "2023-01-28T00:00:00.0000000Z",
    "resourceData": {
      "@odata.id": "solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}",
      "@odata.type": "#microsoft.graph.virtualEvent",
      "id": "solutions/virtualEvents/events/{eventId}/virtualEventWebinar/registration/registrants/{registrantId}"
    }
  }]
}
```