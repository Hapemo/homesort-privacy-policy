# HomeSort privacy policy (beta draft)

_Last updated: 2026-05-07 — beta version. This document is a working draft;
get a lawyer to review it before you take payment from anyone or expand
beyond friends and family._

## What HomeSort does

HomeSort lets you photograph and catalogue items in your home — what they
are, where they are, when you bought them. Everything works offline.
You can optionally create an account to sync your inventory across your
own devices.

## What we collect

If you **do not create an account**, we collect nothing. Your data
never leaves your device.

If you **create an account**, we receive:

- The email address you sign up with.
- The encrypted ciphertext of your inventory records (items, places, tags)
  and your photos.
- The technical metadata needed to coordinate sync: a per-record id,
  the type of record (`item` / `place` / `tag`), an `updated_at`
  timestamp, and a per-device identifier we generate at first launch.
- A device label and platform string for the "your devices" list (e.g.
  "Pixel 7", "android"). Device names default to a generic OS string;
  we never read your phone's actual model unless you edit the label.
- The IP address your sign-in / sign-up requests originate from. This
  is logged by our hosting provider (Supabase) for abuse prevention.

We do **not** receive:

- The plaintext contents of your records (item names, notes, photos).
  These are encrypted on your device before they're uploaded.
- Your password. Sign-in derives a key from your password locally; only
  the derived key (and only after further encryption) is sent to us.

## How encryption works

We describe the encryption as **"encrypted, with email-based recovery"**
rather than as full end-to-end encryption (E2EE), because the design
explicitly trades a small amount of E2EE strength for a much better
"forgot password" experience.

In a nutshell:

1. When you sign up, your device generates a random **data key**.
   Every item, place, tag, and photo you create is encrypted with this
   key before it leaves your device.
2. The data key is stored on our servers in two encrypted forms:
   - One copy is encrypted under a key derived from your password. Only
     you can decrypt this copy.
   - One copy is encrypted under a server-side **escrow key**. Only a
     specific server-side function (the password-reset function) can
     decrypt this copy, and only when you've proven you own the email
     address by clicking a reset link.
3. If you forget your password, the password-reset function uses the
   escrow copy to recover your data key without needing the old
   password.

This means: a leaked database alone does **not** expose your data
(both copies are ciphertext). A leaked database **plus** the server-side
escrow key would. We protect that escrow key with the same care we
protect any other production secret. If you want stronger guarantees
than this — i.e. genuine E2EE where forgotten passwords mean lost data —
HomeSort isn't the right tool for you yet.

The crypto details, for the curious or the auditor:

- Data key wrapping and record encryption: XChaCha20-Poly1305 AEAD.
- Password-to-key derivation: Argon2id (memory 64 MiB, 3 iterations).
- Escrow envelope: X25519 ECDH + HKDF-SHA-256 + XChaCha20-Poly1305.
- Photo encryption: each photo gets a fresh random key; the per-photo
  key lives inside the encrypted record that references the photo, so
  it never crosses the wire in plaintext.

## What we do with the data

We use the data only to:

- Authenticate you when you sign in.
- Synchronise your inventory across your own devices.
- Show you your devices list and let you revoke a device.
- Send you the email confirmation and password-reset emails.

We do **not**:

- Sell, share, or rent your data.
- Use your data for advertising or analytics.
- Train any kind of model on it.
- Look at it ourselves. We literally cannot — the contents are
  encrypted.

## Where the data lives

The encrypted blobs and your account record live in a Supabase project
hosted in the AWS region you'll find listed in the app under
`Settings → Account → Server`. Photos are stored in Supabase Storage
in the same region.

## How long we keep it

For as long as your account is active. If you delete your account, we
delete your row in our auth table; cascading deletes remove your
profile row, sync log, photo blobs, and device registrations. We
typically remove backup copies within 30 days of account deletion.

## Your rights

You can:

- Export your encrypted blobs at any time by syncing them to a second
  device you control.
- Delete your account, which removes our copy of your data.
- Ask us to send you whatever data we have associated with your email.
  Email **jazz.teohyj@outlook.com** (replace this with your real address
  before going public).

If you're in the EU / UK, you have additional rights under GDPR. We
honour them — emailing the support address triggers the same delete /
export workflow.

## Security caveats we're being honest about

- The escrow recovery model means we (or anyone with access to our
  server-side escrow key) could in principle decrypt your data. We
  designed it this way deliberately so you don't lose your inventory if
  you forget your password.
- The "device id" we use for sync is generated client-side; it lets us
  tell devices apart but it isn't a unique fingerprint of your
  hardware.
- We don't currently support pure-E2EE mode. If we ever add it, it'll
  be opt-in, and choosing it will mean a forgotten password = lost data.

## Changes

We'll update this document if the storage model or encryption scheme
changes. The "Last updated" date at the top moves with each change.
For material changes, we'll send an email to active accounts before
the change takes effect.

## Contact

jazz.teohyj@outlook.com (replace before launch).

---

_Internal note for the maintainer: this is a beta draft, not a
substitute for a real privacy policy. Things that need a lawyer's
input before you let anyone outside friends-and-family use HomeSort
include: the GDPR data-protection-officer designation, the precise
legal basis for processing under GDPR Article 6, the international
data transfer language (if you have non-EU users hitting EU servers
or vice versa), the data retention schedule, and the precise
deletion timeline. None of those affect engineering work, but they
absolutely affect "can I open this beta to the public" — handle
before you do._
