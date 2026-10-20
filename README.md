# Ticket Wizard

My final-year Software Engineering project at the University of Westminster.

Ticket Wizard is a music event ticketing website with customer accounts, guest checkout, QR-code tickets and a promoter dashboard.

It demonstrates the main booking flow, with further work needed around payment verification, ticket availability and some dashboard features.

## Features

- Browse and search music events.
- Filter events by location, date and genre.
- Select ticket types and quantities.
- View ticket prices and a separate 10% booking fee.
- Customer registration, login and email-address updates.
- Guest checkout.
- Stripe Checkout integration.
- Order history for registered customers.
- QR-code ticket generation.
- PDF ticket downloads for authorised signed-in users.
- Promoter event creation and editing.
- Promoter sales summaries and ticket-ID validation.
- Password-reset and ticket emails when email credentials are configured.
- A keyword-based help and FAQ section.

The backend also has an event-deletion endpoint, but the dashboard Delete button still needs to be connected to it.

## Technologies

- **Frontend:** HTML, CSS, JavaScript and Lucide icons.
- **Backend:** Node.js and Express.js.
- **Database:** MongoDB Atlas and Mongoose.
- **Authentication:** bcryptjs and express-session.
- **Payments:** Stripe.
- **Tickets:** PDFKit and QRCode.
- **Email:** Nodemailer.
- **Hosting:** GitHub Pages and Render.

## Application Links

- [Website](https://alexandru-rx.github.io/w1887688-Ticket-Wizard/)
- [Backend](https://w1887688-software-final-project.onrender.com)
- [Backend Health Check](https://w1887688-software-final-project.onrender.com/api/ping)

This is a university project, not a production ticketing service. Use Stripe test mode for development.

## Running Locally

### 1. Install the Dependencies

Install Node.js and npm. The current Mongoose dependency requires Node.js 20.19 or later.

```bash
git clone https://github.com/alexandru-rx/w1887688-Ticket-Wizard.git
cd w1887688-Ticket-Wizard/backend
npm ci
```

### 2. Configure the Backend

Create a `.env` file inside `backend/`:

```dotenv
PORT=5000
MONGO_URI=your_mongodb_connection_string
SESSION_SECRET=your_long_random_session_secret
NODE_ENV=development
FRONTEND_URL=http://localhost:5500/docs
STRIPE_SECRET_KEY=your_stripe_test_secret_key
```

Replace the placeholders with your own values. The backend initialises Stripe when it starts, so provide your own Stripe test secret key.

For password-reset and ticket emails, also configure:

```dotenv
EMAIL_USER=your_gmail_address
EMAIL_PASS=your_gmail_app_password
```

The current email utilities use Gmail. Without working email credentials, password-reset emails fail and ticket emails are not delivered.

Keep `.env` out of GitHub. Ensure your `.gitignore` excludes:

```gitignore
.env
node_modules/
```

### 3. Configure the Frontend

In `docs/js/config.js`, change the API address for local development:

```js
window.API_BASE = window.API_BASE || "http://localhost:5000";
var API_BASE = window.API_BASE;
```

The committed configuration points at the hosted Render backend.

### 4. Start the Backend

From the `backend/` folder:

```bash
npm start
```

Check:

```text
http://localhost:5000/api/ping
```

### 5. Start the Frontend

Open the repository root in VS Code and use Live Server on port `5500`.

Open:

```text
http://localhost:5500/docs/index.html
```

Use `localhost` for both frontend and backend.

If your static server serves `docs/` directly, open:

```text
http://localhost:5500/index.html
```

Then change the backend setting to:

```dotenv
FRONTEND_URL=http://localhost:5500
```

Restart the backend after changing `.env`.

### 6. Explore the Website

1. Browse events and select a ticket type and quantity.
2. Register, log in or enter guest details.
3. Review the booking fee and total.
4. Explore Stripe Checkout using test mode.
5. For registered customers, open order history and view tickets.

Passwords require at least eight characters, uppercase and lowercase letters, a number and a special character.

New accounts are created as customers. Promoter access requires a development account with its database role set to `promoter`.

A new database has no events. Add events through a development promoter account.

## Project Structure

```text
backend/
  models/       User, event, order and ticket models
  routes/       Authentication, events, orders and payments
  utils/        Password-reset and ticket email utilities
  server.js     Express application and database connection

docs/
  js/           Frontend logic and API configuration
  index.html    Main website
  style.css     Styling
```

## Main API Routes

| Route | Purpose |
| --- | --- |
| `/api/auth` | Registration, login, logout, account details and password reset |
| `/api/events` | Event listing and promoter event management |
| `/api/payments/create-checkout-session` | Stripe Checkout session creation |
| `/api/orders` | Order creation and ticket generation |
| `/api/orders/my` | Current customer's order history |
| `/api/orders/:orderId/tickets` | Tickets belonging to an owned order |
| `/api/orders/tickets/:ticketId/pdf` | Authorised PDF download |
| `/api/orders/tickets/scan` | Promoter ticket-ID validation |
| `/api/orders/promoter/stats` | Order data for promoter analytics |

## Known Limitations

### Payments and Orders

- Stripe payment is not verified by the backend before an order is marked paid.
- Repeated requests can create duplicate orders.
- Event details, prices and totals need validation against database records.
- Quantities need whole-number checks and a server-side maximum.
- The success page can show confirmation even when order creation fails.
- Order and ticket creation are not transactional, so failures can leave incomplete orders.
- Ticket email failures are logged but not clearly reported to the customer.
- Free tickets are not supported by the current checkout handler.

### Availability

- Existing orders are checked against capacity supplied by the browser rather than the stored event capacity.
- Missing or zero capacity skips the availability check.
- Concurrent orders can oversell because stock checks and order creation are separate operations.
- Tickets are not reserved before payment.
- Stock calculations do not filter orders by payment status.

### Promoter Dashboard and Tickets

- The dashboard Delete button changes local state only and does not call the backend deletion endpoint.
- Promoters are not restricted to their own events, tickets or sales data.
- Event updates need stronger validation.
- Ticket-type fields are locked when editing events through the interface.
- Concurrent ticket validations can accept the same ticket.
- Validation does not check the event date or start time.
- QR payloads are not directly connected to the ticket-ID validation flow; the interface currently uses manual ticket IDs.
- Guest PDF downloads and guest order recovery are not supported.
- Sales figures represent recorded orders, not verified Stripe revenue, and include booking fees.

### Accounts and Security

- Sessions use memory storage and can be lost when the backend restarts.
- Login does not regenerate the session, and password resets do not invalidate existing sessions.
- Password-reset tokens are stored unhashed.
- Authentication and password-reset endpoints need rate limiting.
- Explicit CSRF protection remains to be added.
- User-provided content needs safe rendering instead of direct HTML insertion.
- Account editing currently updates email only, without email-format or ownership verification.

### Interface and Setup

- Selecting today's date currently behaves as All Dates.
- Main genres and subgenres need consistent storage for reliable filtering.
- Ticket and order date displays do not incorporate the separately stored event start time.
- The Privacy Policy Back button points to a missing page.
- Support provides FAQ responses rather than a live support or refund service.
- API addresses and email settings require manual configuration.
- The development script references nodemon, which is not declared as a dependency.
- No automated test suite is included.

## Further Development

The main priorities are verified payment handling, reliable inventory updates, clearer checkout errors, event ownership checks and safer rendering of user content.

This project is part of my portfolio and an opportunity to continue improving my frontend and backend development skills.
