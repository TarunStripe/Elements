# 💳 Stripe Integration with OutSystems – Payment Flow Demo

This project demonstrates how to integrate **Stripe Elements** with an **OutSystems application** to securely accept card payments using **Stripe’s Payment Intents API**.

---

## 🔧 How to Build, Configure, and Run

1. **Get Stripe API Keys**:
   - Sign up at [https://dashboard.stripe.com](https://dashboard.stripe.com)
   - Get your **Publishable Key** and **Secret Key**

2. **In OutSystems**:
   - Use a **REST API** to `POST https://api.stripe.com/v1/payment_intents`
     - Headers: `Authorization: Bearer YOUR_SECRET_KEY`
     - Body: `amount`, `currency`, `automatic_payment_methods[enabled]=true`
   - Extract the `client_secret` from the response.

3. **Frontend Logic (modify existing app)**:
   - **ShoppingCart -> CheckOut Button -> Redirect to StripeCheckout page** – onReady:
     - Call the PaymentIntent REST API
     - Dynamically load Stripe.js
     - Mount the `CardElement` on an empty cardElement

   - **Pay** – on click:
     - Use `stripe.confirmCardPayment(clientSecret, { payment_method: { card: window.card } })`
     - Handle the result, show success or error to user

4. **Test Cards**:
   - Use test card `4242 4242 4242 4242` with any valid expiry & CVC

---

## 🧠 How the Solution Works

- **Stripe APIs Used**:
  - `POST /v1/payment_intents`
  - `confirmCardPayment` from Stripe.js (not a direct API call)

- **Architecture**:
  - Stripe.js is dynamically loaded via client-side JavaScript
  - `CardElement` is rendered securely using `stripe.elements()`
  - The app handles both creation and confirmation of payments on the frontend

---

## 📤 Input / Output Parameters

| Parameter           | Type     | Description                                    |
|---------------------|----------|------------------------------------------------|
| `clientSecret`      | Text     | Secret from PaymentIntent creation             |
| `paymentIntentId`   | Text     | Optional tracking/logging purpose              |
| `amount`            | Integer  | In cents (e.g., 500 = $5.00)                   |
| `result.success`    | Boolean  | Payment success/failure flag                   |
| `result.message`    | Text     | Stripe error message or confirmation message   |

---

## 🚫 Common Issues & Fixes

| Issue / Error                                   | Cause                                                  | Fix                                                                 |
|--------------------------------------------------|---------------------------------------------------------|----------------------------------------------------------------------|
| `Stripe is not defined`                         | Stripe.js not loaded before use                         | Dynamically load Stripe.js first before calling `Stripe()`          |
| `Element already mounted` or `contains child nodes` | Re-mounting `CardElement` on a non-empty container     | Use a clean `<div>` with no children to mount the element           |
| `confirmCardPayment` fails silently             | `card` element not assigned or mounted incorrectly      | Ensure `window.card = elements.create('card')` and `card.mount()`   |
| `404 /v1/payment_intents/.../confirm`           | Called confirm endpoint directly instead of client lib  | Use `stripe.confirmCardPayment()` in frontend, not a REST call      |

---

## 📚 Stripe Reference Links

- [Stripe Elements](https://stripe.com/docs/elements)
- [Stripe Payment Intents API](https://stripe.com/docs/payments/payment-intents)
- [Stripe Testing Cards](https://stripe.com/docs/testing)

---

## 📈 Future Enhancements

- Add support for more payment methods (e.g., PayNow, GrabPay, FPX)
- UI validation for card input fields
- Handle all API errors and response codes gracefully
- Add retry logic & use [idempotency keys](https://stripe.com/docs/idempotency) for robustness
- Logging and better success/failure UX flows

