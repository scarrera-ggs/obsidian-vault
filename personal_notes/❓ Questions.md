  
### To Ask

- Discuss about AP-812.

### Updates on Pwrcell Adapter QA

- I manually changed the CES secrets to use enbala account to authenticate with CES API. This idea came from a conversation I had with Maria from CES about some requests to CES API having authorizations errors - `401` .
- The asset `Enbala Testing Inverter (Neurio Office)` on concerto-adapters.qa stop showing telemetry despite telemetry is successfully received from kinesis_stream. Due to a weird behaviour on ConcertoGateway - Still trying to understand why.
- 