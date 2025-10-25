# App Flow Document for codeguide-tours-booking-platform

## Onboarding and Sign-In/Sign-Up

When a brand new visitor lands on the application’s homepage, they are greeted by a visually engaging landing page that showcases featured destinations, a prominent search bar, and clear calls to action for signing up or logging in. If the visitor chooses to create an account, they click on the Sign Up button and see a page prompting them to enter their email address and create a password. They also have the option to register using a social login provider such as Google or Apple. After submitting their information, the system sends an email verification link. Once they verify their address, they are automatically redirected to their personal dashboard.

Existing users can sign in by clicking the Log In link on the landing page or header. They enter their registered email and password or choose the same social login provider they used during registration. If they have forgotten their password, they click the Forgot Password link, enter their email address on the recovery page, and receive a reset link. After setting a new password, they return to the login page and gain access to their account. When signed in, users see a profile icon in the header that they can click to access a dropdown menu, which includes an option to sign out. Selecting Sign Out immediately ends the session and returns the user to the landing page.

## Main Dashboard or Home Page

After logging in, users arrive at their personalized home dashboard. The header displays the application logo, search bar, and a profile icon. Below the header, a welcome banner greets them by name and highlights recommended tours powered by AI. The left side features a vertical navigation menu with links to Search Tours, My Bookings, Saved Tours, Messages, and Settings. In the main content area, users see a carousel of featured experiences, a section showing their upcoming trips, and quick filters for popular destinations or dates. At the bottom of the screen, a footer provides links to support, terms of service, and privacy policy. Every element on this page is clickable and leads to its respective section, ensuring straightforward navigation.

## Detailed Feature Flows and Page Transitions

### Searching for Tours
When a user wants to find a new experience, they can use the search bar at the top of the page or click Search Tours in the navigation menu. This action takes them to the search page where they enter a destination, travel dates, and number of participants. After clicking the Search button, the app transitions to the search results page. Here, the user can refine their results by price range, rating, tour type, and language using on-screen filters. Each tour appears as a clickable card showing a thumbnail image, title, short description, price, and rating.

### Viewing Tour Details
Clicking on any tour card directs the user to the tour detail page. At the top, a slideshow of high-resolution images showcases the experience. Below the gallery, the tour title, host name, overall rating, number of reviews, and price per person are clearly displayed. Further down, the user finds a detailed itinerary, cancellation policy, and frequently asked questions. A calendar widget allows them to pick a date, and a dropdown lets them select the number of travelers. When ready, they click the Book Now button to proceed.

### Booking a Tour and Payment
Once Book Now is selected, the user lands on a booking summary page that outlines the tour name, date, number of participants, individual cost, and total price including taxes and fees. A prominent Continue to Payment button advances the process. On the payment page, the user enters their card details directly into a secure Stripe-powered form. After confirming payment, the application shows a loading state while communicating with Stripe. If the transaction succeeds, a confirmation page appears with a booking reference, summary details, next steps, and an option to add the trip to their calendar. In the background, a webhook endpoint captures the Stripe event and saves the booking record in the database.

### Viewing Bookings and Itinerary
Users access their Future and Past Trips section by clicking My Bookings in the navigation menu. This page lists each booking with its date, status, and a thumbnail image. Clicking on a booking brings up a detailed view showing the tour itinerary, meeting instructions, host contact information, and options for modifying or canceling the reservation within the allowed window.

### Supplier Dashboard for Tour Operators
Users with operator privileges click on the Switch to Operator button in the profile dropdown. They then see a Supplier Dashboard where their tours are listed in a table showing title, available dates, price range, and booking count. Creating a new tour opens a multi-step form where they enter the tour name, description, photos, pricing, schedule, and capacity. They submit this form and receive immediate feedback on success. Editing or deactivating an existing tour is done by clicking the corresponding action buttons next to each tour in the list.

## Settings and Account Management

Users update their personal profile by clicking Settings in the main navigation. On the Profile tab, they can change their name, email address, phone number, and upload a new profile picture. The Password tab allows them to modify their password by entering the current one and choosing a new password. The Notifications tab contains toggles for email alerts, promotional offers, booking reminders, and host messages. Under the Billing tab, users see their saved payment methods and upcoming subscription or package invoices. They can add or remove cards and download billing statements. A Save Changes button at the bottom of each section commits their updates and returns them to the main app view.

## Error States and Alternate Paths

If a user submits a form with invalid input, such as an incorrectly formatted email or missing required field, an inline error message appears next to the offending field with a brief explanation. Network connectivity issues trigger a full-screen error overlay with a Retry button. When Stripe payment fails, the payment form presents a clear error message, resets the card fields, and invites the user to try again or use a different method. Attempting to access a protected page without a valid session automatically redirects the user to the login page with a notice that they must sign in first. Hitting an undefined URL path displays a friendly 404 page that includes a search bar and a link back to the home dashboard.

## Conclusion and Overall App Journey

A user’s journey begins at the landing page where they sign up or log in. They then explore recommended and searchable tours from the home dashboard. After finding an experience they like, they view the tour detail page, choose dates and party size, and proceed through the secure Stripe payment flow. Upon successful payment, the booking is confirmed and recorded in their upcoming trips. Users manage their profile, payment methods, and notifications in Settings. Tour operators seamlessly switch to a dedicated dashboard to create and manage their offerings. Throughout every step, clear feedback, error handling, and recovery options ensure a smooth end-to-end experience from initial discovery to post-trip follow-up.