<div align="center">

![ReleaseFrame, one prompt and a full screenshot set in every language](assets/hero.png)

# ReleaseFrame

**App Store screenshots, designed by AI.**

Stop wrestling with Figma the night before you ship. ReleaseFrame generates polished App Store screenshot carousels from a description of your app, then lets you tweak them in plain English.

[**releaseframe.com**](https://releaseframe.com) ·
[**Download for Mac**](https://github.com/simonegiammy/ReleaseFrame/releases/latest) ·
[Open the web app](https://releaseframe.com)

</div>

---

## Two ways to use it

**Native macOS app**, one time lifetime license. Bring your own Claude or Codex agent for unlimited generations. Push finished screenshots straight to App Store Connect from the app, no zip and drag-and-drop dance. Requires macOS 14 or newer.

**Web app**, runs in any browser. Subscription tiers (Free, Pro, Ultimate), same generation flow, same export quality.

Both share the same design engine, so anything you produce in one looks identical in the other.

## What it does

- Takes a short description of your app and proposes a full screenshot story: hook, features, social proof, call to action.
- Generates the right pixel sizes for every device class (iPhone 6.7", 6.5", iPad 12.9", and more).
- Lets you edit each slide with natural language prompts, like "make the headline shorter and the screenshot bigger".
- Exports clean, App Store ready images, or pushes them directly to App Store Connect.
- Localizes the same story across multiple languages in one click.

## How it's built

I run ReleaseFrame solo, so the whole thing is built to ship often and break rarely. A few parts I'm happy with:

**The macOS release pipeline.** The app is distributed outside the App Store, which means I own every step that Apple normally hides behind App Store Connect. One script takes a version number and runs the full chain end to end: archive, Developer ID export, Apple notarization through notarytool, Sparkle update signing, publishing the GitHub release, and regenerating the Sparkle appcast that the app reads to update itself. The appcast is served from GitHub Pages, so there is no server to babysit and updates cost nothing to host.

**Talking to App Store Connect directly.** Instead of exporting a zip and uploading by hand, the app signs its own ES256 JWTs from the user's App Store Connect key (kept in the macOS Keychain) and calls the App Store Connect API to push screenshots straight up. Getting the JWT signing and the upload flow right was the fiddly part, and it is the feature that saves the most time in practice.

**Bring your own agent.** Rather than reselling tokens, the Mac app plugs into the user's existing Claude or Codex agent. Generations stay unlimited for them and predictable for me, and nobody pays a margin on top of model costs. Built in AI touches use the Gemini Developer API.

**Server side image rendering.** Screenshot frames are rendered from JSX to SVG to PNG with satori, which is what keeps the output pixel identical between the web app and the Mac app from a single source of truth. The renderer is strict about layout, so a good chunk of the work was building guardrails that catch bad templates early instead of producing a broken image.

**Two payment models, one product.** Web subscriptions run on Stripe with checkout, the customer portal, webhooks, and a weekly scheduled function that keeps prices in sync. Mac lifetime licenses go through LemonSqueezy as merchant of record, which handles VAT and invoicing for me. Transactional email is sent through Resend.

**One command go-live for the web.** Shipping the web app is a single script that runs preflight checks, verifies that every required secret is present, builds the frontend and the functions, deploys Firestore rules, indexes, storage and Cloud Functions, smoke tests the Stripe path, then merges to main to trigger the Vercel deploy. It refuses to push if anything looks off.

### Stack at a glance

- **macOS app**: SwiftUI, Developer ID plus notarization, Sparkle auto updates, App Store Connect API integration, Firebase for analytics and Gemini for built in AI.
- **Web app**: Vite and TanStack Start on Vercel, backend on Firebase (Firestore, Cloud Storage, Cloud Functions), Stripe for subscriptions, Resend for email.
- **Shared**: a single design engine and a satori based renderer so both surfaces produce the same output.

## About this repository

This repo is the public release home for the macOS app. It hosts the signed and Apple notarized DMGs (see [Releases](https://github.com/simonegiammy/ReleaseFrame/releases)) and the Sparkle appcast that powers in-app updates. The product source code lives in private repositories, the notes above are the short tour for anyone curious about how it fits together.

## Built by

[Simone Giammusso](https://github.com/simonegiammy), an indie dev who got tired of remaking screenshots by hand.

Questions, feedback, or "this is broken on my machine" reports go to **simone@releaseframe.com**. Replies guaranteed, it's just me.
