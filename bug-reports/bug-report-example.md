# Bug Report: Transfer Confirmation Shows Incorrect Recipient Name

## Summary
After completing a successful internal transfer, the confirmation screen displays the sender's name instead of the recipient's name, even though the correct account was credited.

## Environment
- Platform: Web (Chrome 126, macOS)
- Environment: Staging
- Build: v2.4.1

## Severity / Priority
- Severity: Medium (incorrect display, not a financial impact)
- Priority: High (directly affects user trust and transaction clarity)

## Steps to Reproduce
1. Log in as User A
2. Navigate to Transfers > Send Money
3. Select Account B (a different user) as recipient
4. Enter transfer amount ($50) and submit
5. Observe the confirmation screen

## Expected Result
Confirmation screen should display:
"You sent $50 to [Recipient Name — User B]"

## Actual Result
Confirmation screen displays:
"You sent $50 to [Sender Name — User A]"

The transaction itself is correct (verified via transaction history and account balances) — this is a display-only defect on the confirmation screen.

## Evidence
- Screenshot: confirmation-screen-bug.png (attached in original ticket)
- Transaction history showing correct recipient: transaction-log.png (attached in original ticket)

## Additional Notes
- Bug does not occur on external transfers, only internal (same-bank) transfers
- Did not reproduce on mobile app — appears to be web-specific
- Likely a front-end mapping issue pulling the wrong name field from the transfer response object

## Suggested Next Step
Check the front-end confirmation component to confirm it is reading `recipient.name` rather than `sender.name` from the API response payload.
