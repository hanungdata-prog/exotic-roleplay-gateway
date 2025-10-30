# Exotic Roleplay Gateway App Flow Document

## Onboarding and Sign-In/Sign-Up

When a new visitor arrives at the Exotic Roleplay Gateway, they land on a public welcome page that introduces the platform and highlights its key benefits. This landing page offers a prominent button inviting users to create an account or sign in if they already have one. Clicking “Sign Up” brings the user to a registration form where they enter their email address, choose a password, and submit. The system validates the input on the client side, then sends the data to the backend API endpoint for account creation. Upon success, the user receives a confirmation email, and the application redirects them to a brief success screen before automatically signing them in.

If a user chooses “Sign In,” they see a form requesting their email and password. When they submit, the application calls the authentication API to verify credentials. A successful response sets a secure session cookie and navigates the user to the protected dashboard. If authentication fails, the user sees a clear error message explaining whether the email is unregistered or the password is incorrect. A link on this page allows users to recover a forgotten password. Clicking “Forgot Password” asks for the registered email, triggers a reset link to the user’s inbox, and displays a confirmation message instructing them to check their email. After successfully resetting their password, they return to the sign-in form and log in with their new credentials. Signing out at any time is accessible through a button in the user’s profile menu in the header, which clears the session and routes them back to the public landing page.

## Main Dashboard or Home Page

After logging in, the user arrives at the main dashboard. This protected home page features a persistent header and a vertical sidebar. The header displays the application logo on the left, a theme toggle button for light or dark mode in the center, and the user’s avatar with a dropdown menu on the right. The dropdown menu provides quick links to account settings and a sign-out option. The sidebar lists navigation items organized by functionality, such as Dashboard, Profile Settings, and any custom modules added later. The central content area of the dashboard greets the user by name and shows an overview card summarizing recent activity or key metrics.

From this home page, the user can click on any sidebar link to load a new content view within the same layout. Each section replaces the main content area without reloading the header or sidebar. This layout pattern ensures consistent navigation and branding across the application.

## Detailed Feature Flows and Page Transitions

The core feature of the Exotic Roleplay Gateway is its authentication and user gating system. When the user submits sign-up or sign-in forms, the frontend captures form data and sends a POST request to the `/api/auth/sign-up` or `/api/auth/sign-in` endpoint. The backend uses Better Auth to handle the request, performing validation, hashing passwords, creating or verifying user records in the PostgreSQL database via Drizzle ORM, and returning success or error responses. Upon success, the frontend triggers a client-side router push to the dashboard route.

Within the dashboard, the user may navigate to their profile page by clicking the Profile Settings link. This page loads a form prefilled with user data retrieved from the `/api/user` endpoint. When the user updates fields like display name or email preferences and submits the form, the application sends a PATCH request to `/api/user`. The backend validates the updates, saves changes in the database, and returns the updated user object. The form then displays a confirmation message before redirecting the user back to the dashboard overview.

If the application is extended with additional verification steps—for example, integrating a Discord verification flow—the user might click a “Verify with Discord” button in the dashboard. This action opens a dedicated verification page where the user follows an OAuth handshake or submits a CAPTCHA. The frontend sends the verification token to a custom `/api/verify` endpoint. The server verifies the token, updates the user’s verification status in the database, and optionally sends a success webhook to a Discord channel. After verification, the user sees a success screen that automatically returns them to the main dashboard with an updated badge indicating their verified status.

All pages within the application share the same header and sidebar, ensuring smooth transitions. Navigating from one module to another only replaces the main content area, preserving context and reducing load times. Errors or loading indicators appear inline in the content area to keep the user focused on the active task.

## Settings and Account Management

The user’s account settings are accessible via the profile dropdown in the header or through the sidebar’s Profile Settings link. On the settings page, the user can update personal information such as their email, display name, and password. The page also allows toggling notification preferences and choosing a default theme mode. Each change triggers an API request to the backend, which validates and persists the new values. Success and error messages display directly above the form so the user knows whether their updates succeeded or encountered validation issues.

If the application includes subscription or billing features, a Billing Settings tab will appear alongside profile settings. Within that tab, users can view their current plan, enter payment details to upgrade or downgrade, and review recent invoices. All billing operations call the appropriate billing API endpoints, and the page reflects updates in real time after a successful transaction.

After completing any updates in the settings area, the user can return to the main dashboard by clicking the Dashboard link in the sidebar or selecting Dashboard from the profile dropdown. This consistent navigation path ensures users always know how to get back to the primary experience.

## Error States and Alternate Paths

Whenever a user enters invalid information—such as an improperly formatted email, a weak password, or mismatched confirmation fields—the form displays inline validation messages in red text next to the corresponding field. If the backend detects server-side errors, such as an email already in use during sign-up or an incorrect password during sign-in, a prominent alert bar appears above the form describing the issue and possible next steps.

In the event of network connectivity loss or server outages, the application shows a full-width notification at the top of the page informing the user that connectivity is lost. While offline, any form submissions are disabled, and guidance text asks them to check their internet connection. Once the connection is restored, the notification disappears and the user can retry their previous action.

If an authenticated user attempts to access a route without the required permissions or roles, the application redirects them to an Access Denied page within the same layout. This page explains why they cannot view the content and offers a link back to the dashboard.

## Conclusion and Overall App Journey

From the user’s first visit to the landing page through account creation, sign-in, dashboard exploration, profile updates, and optional verification workflows, the Exotic Roleplay Gateway delivers a seamless and consistent experience. Public and private pages share the same branding, while protected routes enforce authentication and authorization checks. Error handling and offline notifications guide users through validation issues or connectivity problems. The modular layout of header, sidebar, and content area ensures stable navigation and quick page transitions. As the user returns day after day, they use a familiar interface to manage their account, verify themselves, and interact with any additional modules you build on this robust starter template.