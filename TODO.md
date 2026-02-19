# TODO - Fix Stats Command

## Phase 1: Update database.js
- [ ] Add cumulative uptime tracking (totalUptime)
- [ ] Add session tracking (currentSessionStart, previousSessionUptime)
- [ ] Update recordObfuscation to track timestamps
- [ ] Add function to start new session
- [ ] Add function to end current session

## Phase 2: Update index.js
- [ ] Add client 'ready' event to start session tracking
- [ ] Call recordObfuscation in handleObfuscation
- [ ] Call recordObfuscation in handleObfuscationWithExtra
- [ ] Call recordObfuscation in handleHeavyObfuscation
- [ ] Update .stats command to show correct stats

## Phase 3: Test
- [ ] Verify obfuscation tracking works
- [ ] Verify uptime tracking works
- [ ] Verify DM/Server tracking works

