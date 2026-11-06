# Sample evaluation: routing support tickets

These are synthetic results, not a live Osmosis run. Two agents classified the same five held-out tickets, once each, using the same exact-match grader and timeout. A timeout produced no prediction and received no grade.

| Ticket | Expected queue | Baseline prediction | Candidate prediction |
| --- | --- | --- | --- |
| Duplicate charge | Billing | Billing | Billing |
| Password reset | Accounts | Accounts | Accounts |
| Late delivery | Shipping | Shipping | Shipping |
| Refund request | Billing | Billing | Timeout |
| Wrong item delivered | Returns | Shipping | Timeout |

Using these rows, show how often each agent was correct among graded predictions, how often it delivered a correct answer across all five tickets, and whether the agents differed on tickets both answered. Explain what to inspect before choosing a model. Offer to repeat the comparison with the user's results.
