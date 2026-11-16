Uses redreactor 0.1.9.

## What’s changed

- Rename add-on to app @mreditor97 (#55)
- Restrict workflow permissions and replace workflow_run trigger @mreditor97 (#56)
- Fix base image drift, exec the service and auto-configure MQTT @mreditor97 (#58)
- Restrict add-on option types to what the service accepts @mreditor97 (#59)
- Add TLS, sensor and discovery options; document new Red Reactor features @mreditor97 (#60)
- Add Home Assistant birth message topic and payload options @mreditor97 (#61)
- Drop the unused SYS_ADMIN capability @mreditor97 (#62)
- Release stable, beta and edge apps from redreactor releases @mreditor97 (#63)
- Migrate the release-drafter config off deprecated fields @mreditor97 (#64)
- Name app releases with a leading v and add What's changed notes @mreditor97 (#65)
- Push the release pin with DISPATCH_TOKEN @mreditor97 (#66)
- Label every app option in the translations, with TLS capitalised @mreditor97 (#67)
- Give the release pin commit a subject line only @mreditor97 (#68)
- Add the battery settings to the app configuration @mreditor97 (#69)
- Move the s6 user bundle to user-bundles.d @mreditor97 (#70)
- Use the Samsung INR18650-35E as the battery curve example in the docs @mreditor97 (#71)
- 🔧 Tighten the options schema to the service's start-up limits @mreditor97 (#73)
- Add the new redreactor options, and check the schema against the service @mreditor97 (#74)
- Start Red Reactor again when the shutdown didn't work @mreditor97 (#77)

## ⬆️ Dependency updates

- ⬆️ Update redreactor to v0.1.8 @[renovate[bot]](https://github.com/apps/renovate) (#46)
- ⬆️ Update home-assistant/cli to v5.2.0 @[renovate[bot]](https://github.com/apps/renovate) (#47)
- ⬆️ Update home-assistant/cli to v5.3.0 @[renovate[bot]](https://github.com/apps/renovate) (#48)
- ⬆️ Update home-assistant/cli to v5.3.1 @[renovate[bot]](https://github.com/apps/renovate) (#49)
- ⬆️ Update home-assistant/cli to v5.4.0 @[renovate[bot]](https://github.com/apps/renovate) (#50)
- ⬆️ Update home-assistant/cli to v5.5.0 @[renovate[bot]](https://github.com/apps/renovate) (#51)
- ⬆️ Update home-assistant/cli to v5.6.0 @[renovate[bot]](https://github.com/apps/renovate) (#57)
- ⬆️ Update ghcr.io/home-assistant/aarch64-base-python Docker tag to v3.15 @[renovate[bot]](https://github.com/apps/renovate) (#72)
- ⬆️ Update home-assistant/cli to v5.7.0 @[renovate[bot]](https://github.com/apps/renovate) (#75)
- ⬆️ Update home-assistant/cli to v6 @[renovate[bot]](https://github.com/apps/renovate) (#76)

