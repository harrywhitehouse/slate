# Label Statuses

Status | Post action
------ | -----------
created | Label created by user
manifested | Manifest requested by user (or automatically right before the postmark date)
refund_requested | Label refund (delete) requested by user (meter refunds not yet approved)
refund_approved | IB determined labels wasn't used (to become `refunded` within 15 minutes)
refund_rejected | IB determined label was used (refund request rejected)
refunded | For `refund_approved` labels; added refund amount back on the user's account meter
refund_pending_review | Approx. 2 week period for IB to determine if label was used
