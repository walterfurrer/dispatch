## 1. Users and responsibilities

> **CODEX REVIEW — revised:** The unplanned `users` API was removed. The MVP has operational roles, but its first API resources are shipments, drivers, and assignments.

Customers: Provide shipment information only. Not an authenticated user.
Dispatcher: Creates shipments, assigns drivers, and monitors exceptions.
Driver: Reports availability, location, and permitted delivery-progress updates. Driver locations are recorded separately as location-history records, not as profile fields.
Operations Manager: Manages drivers and oversees active operations.

## 2. MVP shipment fields
Identity: id, reference number, creation/update timestamps
Lifecycle: status
Pickup: name, address, contact phone, instructions
Drop-off: recipient name, address, contact phone, delivery instructions
Operational: package description, optional weight, a shipment is related to a driver through an assignment
Audit: cancellation, pickup-failure, or return reason when applicable.

## 3. MVP driver fields
Identity: id, first name, last name, phone, email
Operations: availability - OFF_DUTY, AVAILABLE, or ON_DELIVERY
Audit: creation/update timestamps

## 4. Shipment states and allowed transitions

Terminal states: CANCELLED, PICKUP_FAILED, RETURNED, and DELIVERED

CREATED -> ASSIGNED or CANCELLED
ASSIGNED -> EN_ROUTE_TO_PICKUP or CANCELLED
EN_ROUTE_TO_PICKUP -> PICKED_UP, CANCELLED, or PICKUP_FAILED
PICKED_UP -> IN_TRANSIT or RETURNED
IN_TRANSIT -> DELIVERED or RETURNED
Terminal states have no outbound transitions.

PICKUP_FAILED means the driver could not collect the parcel, so it never
entered courier custody. RETURNED means a previously picked-up parcel was
returned to its pickup location rather than delivered.

Creating an assignment must atomically create the assignment, change the shipment to ASSIGNED, and change the driver to ON_DELIVERY.

> **CODEX REVIEW — relocated:** A dispatcher creates assignments and cancels eligible shipments; the assigned driver performs progress transitions.

## 5. API resources and MVP non-goals

> **CODEX REVIEW — revised:** This section now describes the three agreed MVP resources and removes the unplanned `users` API.

### API resources

- **Shipments:** Delivery requests, including lifecycle, pickup/drop-off data, package details, and cancellation/failure information.
- **Drivers:** Operational courier profiles and their current availability.
- **Assignments:** Records connecting drivers to shipments. Creating an assignment updates the related shipment and driver atomically.

### API route candidates

- `/v1/shipments`
- `/v1/shipments/{shipmentID}`
- `/v1/shipments/{shipmentID}/assignments`
- `/v1/drivers`
- `/v1/drivers/{driverID}`

### MVP non-goals

- Customer accounts, self-service shipment creation, and customer tracking
- Payments, pricing, quotes, and invoicing
- Multi-stop, batch, or split shipments
- Route optimization, ETAs, and live maps
- Mobile apps; driver actions use the API initially
- Notifications, webhooks, and event brokers
- Automatic dispatching, reassignment, and shift scheduling
- Proof of delivery, photos, signatures, and document verification
