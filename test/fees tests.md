# Fee tests
Verifing that fees can be configured, shown, deducted, and itemized for settlement

## Variations

### Fee Source
- Sender fee
- Receiver fee
- Agent cash-out fee
- Agent cash-in fee
- Central fee

### Path for transfer
- Cross-DFSP
- Same DFSP

### Configure Amount
- Stair-step: flat plus percent for range
- Zero

## Test Matrix 
Use pair combinations of the variations we get a matrix like this:

| Path | Source | Receiver | Center| Agent Cash In |  Agent Cash Out |
|------| -------|----------|-------|----------------| --------------- |
|Cross-DFSP |  0 |  Stair-step |  0 |  Stair-step |  0 |
|Cross-DFSP |  Stair-step |  0 |  Stair-step |  0 |  Stair-step |
|Same-DFSP |  Stair-step |  Stair-step |  0 |  0 |  Stair-step |
|Same-DFSP |  0 |  0 |  Stair-step |  Stair-step |  0 |
|* |  0 |  * |  Stair-step |  0 |  Stair-step |
|* |  Stair-step |  * |  0 |  Stair-step |  0 |
|Cross-DFSP |  * |  * |  * |  Stair-step |  Stair-step |
|* |  * |  * |  * |  0 |  0 |

\* value doesn't matter