<wizard-report>
# PostHog post-wizard report

The wizard has completed a deep integration of PostHog analytics into **Recurrly**, an Expo subscription-tracking app. Here is a summary of all changes made:

- **`app.config.js`** (new): Created to replace `app.json` as the Expo config entry point. Reads `POSTHOG_PROJECT_TOKEN` and `POSTHOG_HOST` from environment variables and exposes them via `expo.extra`, making them available to `src/config/posthog.ts` through `expo-constants`.
- **`.env`** (updated): Added `POSTHOG_PROJECT_TOKEN` and `POSTHOG_HOST` with the correct project values. Covered by `.gitignore`.
- **`app/(tabs)/index.tsx`** (updated): Added `subscription_modal_opened` capture when the add-subscription button is pressed. Added `subscription_modal_closed` capture when the modal is dismissed. Removed a duplicate `subscription_created` capture that was also firing in the modal component (preventing double-counting).
- **`app/(tabs)/subscriptions.tsx`** (updated): Added `subscription_searched` capture whenever a non-empty search query is typed on the subscriptions list screen.

Existing coverage was already solid — auth events (`user_signed_up`, `user_signed_in`, `user_sign_up_failed`, `user_sign_in_failed`), user identification with `posthog.identify()`, `posthog.reset()` on sign-out, screen tracking via `posthog.screen()` in the root layout, and subscription events (`subscription_created`, `subscription_expanded`, `subscription_collapsed`, `subscription_details_viewed`) were all already in place.

| Event | Description | File |
|---|---|---|
| `subscription_modal_opened` | User tapped the add button to open the create-subscription modal. | `app/(tabs)/index.tsx` |
| `subscription_modal_closed` | User closed the create-subscription modal (dismissed or after submission). | `app/(tabs)/index.tsx` |
| `subscription_searched` | User entered a non-empty search query on the subscriptions list screen. | `app/(tabs)/subscriptions.tsx` |
| `subscription_created` | New subscription created with name, price, frequency, and category. | `components/CreateSubscriptionModal.tsx` |
| `subscription_expanded` | User expanded a subscription card to see details. | `app/(tabs)/index.tsx` |
| `subscription_collapsed` | User collapsed an expanded subscription card. | `app/(tabs)/index.tsx` |
| `subscription_details_viewed` | User navigated to a subscription detail page. | `app/subscriptions/[id].tsx` |
| `user_signed_up` | User completed email verification and created an account. | `app/(auth)/sign-up.tsx` |
| `user_sign_up_failed` | Sign-up attempt failed with an error. | `app/(auth)/sign-up.tsx` |
| `user_signed_in` | User signed in successfully. | `app/(auth)/sign-in.tsx` |
| `user_sign_in_failed` | Sign-in attempt failed with an error. | `app/(auth)/sign-in.tsx` |
| `user_signed_out` | User signed out from the settings screen. | `app/(tabs)/settings.tsx` |

## Next steps

We've built some insights and a dashboard to keep an eye on user behavior, based on the events we just instrumented:

- [Analytics basics (wizard) Dashboard](https://us.posthog.com/project/490550/dashboard/1773995)
- [Subscription Creations Over Time](https://us.posthog.com/project/490550/insights/9vQYCnfT)
- [User Auth Events](https://us.posthog.com/project/490550/insights/rRhplmoq)
- [Sign-up to Subscription Funnel](https://us.posthog.com/project/490550/insights/0TeC94fD)
- [Modal-to-Subscription Conversion Rate](https://us.posthog.com/project/490550/insights/zwxD06oO)
- [Subscription Engagement](https://us.posthog.com/project/490550/insights/L90KGoRO)

## Verify before merging

- [ ] Run a full production build (the wizard only verified the files it touched) and fix any lint or type errors introduced by the generated code.
- [ ] Run the test suite — call sites that were rewritten or instrumented may need updated mocks or fixtures.
- [ ] Add `POSTHOG_PROJECT_TOKEN` and `POSTHOG_HOST` to `.env.example` and any monorepo/bootstrap scripts so collaborators know what to set.
- [ ] Confirm the returning-visitor path also calls `identify` — a handler that only identifies on fresh login can leave returning sessions on anonymous distinct IDs.

### Agent skill

We've left an agent skill folder in your project. You can use this context for further agent development when using Claude Code. This will help ensure the model provides the most up-to-date approaches for integrating PostHog.

</wizard-report>
