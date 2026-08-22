# CertiChain AI — Architecture Notes

See the main [README](../README.md) for the high-level architecture diagram.

## Database Schema (planned)

- `users` — id, name, email, wallet_address, role, created_at
- `institutions` — id, name, institution_code, wallet_address, verified, created_at
- `credentials` — id, credential_id, holder_id, issuer_id, credential_type, document_hash, storage_uri, blockchain_tx_hash, issued_at, status
- `ai_analysis` — id, credential_id, text_score, visual_score, metadata_score, anomaly_score, final_risk_score, risk_level, model_version, created_at
- `verification_logs` — id, credential_id, verifier_id, verification_result, timestamp, ip_hash

## API (planned)

- `POST /api/credentials/issue`
- `POST /api/credentials/verify`

## AI Risk Signals

- Text/OCR consistency (institution match, degree validity, date validity, CGPA range, certificate ID format)
- Visual forgery detection (font, compression, pixel statistics, anti-aliasing anomalies)
- Metadata analysis (creation vs. modification timestamps, producer/creator fields)

Risk signals are combined into a weighted authenticity score. AI never has final authority — cryptographic hash matching + issuer authorization + revocation status are always checked alongside it.
