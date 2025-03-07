---
title: Managed Ruleset parameters
pcx-content-type: reference
weight: 4
meta:
  title: HTTP DDoS Attack Protection parameters
---

# HTTP DDoS Attack Protection parameters

Configure the HTTP DDoS Attack Protection Managed Ruleset to change the action applied to a given attack or modify the sensitivity level of the detection mechanism. You can [configure the Managed Ruleset in the Cloudflare dashboard](/ddos-protection/managed-rulesets/http/configure-dashboard/) or [define overrides via Rulesets API](/ddos-protection/managed-rulesets/http/configure-api/).

The available parameters are the following:

- [Action](#action)
- [Sensitivity Level](#sensitivity-level)

## Action

API property name: `"action"`.

The action that will be performed for requests that match specific rules of Cloudflare's DDoS mitigation services. The available actions are:

{{<definitions>}}

- **Block**

  - API value: `"block"`.
  - Blocks HTTP requests that match the rule expression.

- **Managed Challenge**

  - API value: `"managed_challenge"`.
  - [Managed Challenges](https://support.cloudflare.com/hc/articles/200170136#managed-challenge) help reduce the lifetimes of human time spent solving Captchas across the Internet. Depending on the characteristics of a request, Cloudflare will dynamically choose the appropriate type of challenge based on specific criteria.

- **Legacy CAPTCHA**

  - API value: `"challenge"`.
  - Presents a CAPTCHA challenge to the clients making HTTP requests that match a rule expression.

- **Log**

    - API value: `"log"`.
    - Only available on Enterprise plans. Logs requests that match the expression of a rule detecting HTTP DDoS attacks. Recommended for validating a rule before committing to a more severe action.

{{<Aside type="note">}}

You cannot configure the rule action to _Log_ for rules with the `gatebot` tag or any rule whose `id` starts with `GB`.

However, you can use the _Log_ action in the global ruleset configuration. In this case, any rule with the `gatebot` tag or whose `id` starts with `GB` will ignore the ruleset configuration and use the default action as defined in the Managed Ruleset. To prevent `gatebot` rules from executing their default action in _Log_ mode, set the sensitivity level of these rules to _Essentially Off_.

{{</Aside>}}

- **Connection Close**

  - API value: _N/A_ (internal rule action that you cannot use in overrides).
  - The client is instructed to establish a new connection (by disabling `keep-alive`) instead of reusing the existing connection. Existing requests are not affected.

- **Force Connection Close**

  - API value: _N/A_ (internal rule action that you cannot use in overrides).
  - Closes ongoing HTTP connections. This action does not block a request, but it forces the client to reconnect. For HTTP/2 and HTTP/3 connections, the connection will be closed even if it breaks other requests running on the same connection.
  - The performed action depends on the HTTP version:

    - – HTTP/1: set the [`Connection` header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Connection#directives) to `close`.
    - – HTTP/2: send a [`GOAWAY` frame](https://datatracker.ietf.org/doc/html/rfc7540#section-6.8) to the client.

- **DDoS Dynamic**
  - API value: _N/A_ (internal rule action that you cannot use in overrides).
  - Performs a specific action according to a set of internal guidelines defined by Cloudflare. The executed action can be one of the above or an undisclosed mitigation action.

{{</definitions>}}

## Sensitivity Level

API property name: `"sensitivity_level"`.

Defines how sensitive a rule is. Affects the thresholds used to determine if an attack should be mitigated. A higher sensitivity level means having a lower threshold, while a lower sensitivity level means having a higher threshold.

The available sensitivity levels are:

| UI value          | API value   |
| ----------------- | ----------- |
| _High_            | `"default"` |
| _Medium_          | `"medium"`  |
| _Low_             | `"low"`     |
| _Essentially Off_ | `"eoff"`    |

You cannot increase the sensitivity level beyond _High_ (`"default"`).

In most cases, when you select the _Essentially Off_ sensitivity level the rule will not trigger for any of the selected actions, including _Log_. However, if the attack is extremely large, Cloudflare's protection systems will still trigger the rule's mitigation action to protect Cloudflare's network.
