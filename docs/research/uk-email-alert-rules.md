# What do UK rules require of email Alerts?

Research for [#4](https://github.com/urfolomeus/cohort-005-project/issues/4) (map [#1](https://github.com/urfolomeus/cohort-005-project/issues/1)). Researched 2026-11-18.

> This is engineering research, not legal advice. Where a point is a judgement call it is marked **Judgement**.

## Answer

- **Alerts are service messages, provided they stay purely informational.** PECR's email marketing rules (reg 22/23) then do not apply, and no marketing consent is needed. **Judgement:** "a new lesson was added to your course" is close to the line. Neutral, factual copy is a service message. Copy that *actively encourages* the Student to come back, or that mentions other courses or offers, turns the Alert into direct marketing.
- **Fallback if an Alert is ever classed as marketing:** the reg 22(3) "soft opt-in" would probably cover it, because Students are existing customers and the Alert concerns a course they bought. That only holds if Cadence (a) offers an opt-out when it collects the email address and (b) includes an opt-out in every Alert. The existing on/off unsubscribe meets (b) if it appears as a link in every Alert. Build it that way and both readings are covered.
- **Click tracking:**
  - Open-tracking pixels fall under PECR reg 6, and per-person tracking does not fit any exception, so pixels need consent. Avoid them.
  - A per-Alert token in the link URL, recorded server-side when the click lands, arguably does not touch the device at all. On that reading only UK GDPR applies (legitimate interests, privacy notice, retention). **Judgement:** the ICO has not ruled on this case directly.
  - Any cookie set or read to measure re-engagement *per Student* is outside the analytics exception and needs consent.
- **Record-keeping:**
  - Keep the Student's Alert on/off preference with a timestamp.
  - Add Alert processing, including click data, to the Article 30 record of processing and the privacy notice.
  - If consent is ever relied on (for marketing or pixels), keep evidence of it (Art 7(1)).

## 1. Service message vs direct marketing

**Law.** PECR reg 22(2) bars "unsolicited communications for the purposes of direct marketing" by email to individual subscribers without prior consent. Reg 23 adds that such mail must not conceal the sender and must give a valid opt-out address. Both rules apply only to *direct marketing*. — [PECR reg 22](https://www.legislation.gov.uk/uksi/2003/2426/regulation/22), [reg 23](https://www.legislation.gov.uk/uksi/2003/2426/regulation/23)

Direct marketing means "the communication (by whatever means) of advertising or marketing material which is directed to particular individuals". The Data (Use and Access) Act 2025 copied this definition from the DPA 2018 into PECR. — [ICO: Identify direct marketing](https://ico.org.uk/for-organisations/direct-marketing-and-privacy-and-electronic-communications/direct-marketing-guidance/identify-direct-marketing/) (updated 20 Aug 2025), [ICO: Key concepts (email)](https://ico.org.uk/for-organisations/direct-marketing-and-privacy-and-electronic-communications/guidance-on-direct-marketing-using-electronic-mail/key-concepts-for-direct-marketing-using-electronic-mail/)

**ICO position on service messages.**
- The rules do "not cover messages sent purely for administrative or customer service purposes (often called 'service messages')", which "do not count as direct marketing if they only provide administrative information and do not promote anything". — Key concepts (above)
- "Routine customer service messages" that give customers information they need about a current contract or past purchase are not marketing. — [ICO Guide to PECR: electronic and telephone marketing](https://ico.org.uk/for-organisations/direct-marketing-and-privacy-and-electronic-communications/guide-to-pecr/electronic-and-telephone-marketing/)
- General branding and logos do not make a message marketing. — Identify direct marketing
- A message that "actively promotes or encourages people to make use of a particular service, special offer, or upgrade" is likely to be direct marketing, even if promotion is only a secondary purpose. The gym example: a payment-failure call that also pitches personal training. — Identify direct marketing
- "Simply using a neutral tone doesn't necessarily avoid messages being direct marketing"; context matters. A neutral reminder about an existing benefit, without encouragement to use it, is more likely a service message. — Identify direct marketing

**Applying it to Alerts.**

| Alert | Likely classification |
| --- | --- |
| "An instructor answered your question" | Service message. It answers the Student's own request. |
| "A new lesson was added to *Course X* (which you own)" | **Judgement:** service message if worded factually. It tells the Student about content they already paid for. It drifts towards marketing if worded as "Don't fall behind, jump back in!" |
| Any Alert that mentions other courses, discounts, upsells, "you might also like" | Direct marketing. PECR reg 22/23 apply. |

The ICO's guidance does not mention "new content available" notifications directly. The classification above applies its general tests.

## 2. Consent and lawful basis

- **PECR:** a service message needs no PECR consent.
- **If an Alert is classed as marketing:** reg 22(3) soft opt-in lets you email without consent if all of these hold:
  - contact details were obtained "in the course of the sale or negotiations for the sale" of a product or service;
  - the marketing is for "similar products and services";
  - the recipient was given "a simple means of refusing" use of their details, both at collection and in every subsequent message.
  - Source: [PECR reg 22(3)](https://www.legislation.gov.uk/uksi/2003/2426/regulation/22)
  - Cadence's Students are purchasers, so this route is realistic. **Judgement:** it depends on Cadence's signup flow offering the opt-out at collection.
- **UK GDPR:** sending an Alert processes personal data (email address, course activity).
  - The natural basis is legitimate interests ([Art 6(1)(f)](https://www.legislation.gov.uk/eur/2016/679/article/6)), or arguably contract (Art 6(1)(b)) for messages directly tied to the purchased service.
  - **Judgement:** legitimate interests is the safer fit for optional notifications, because Students can switch them off. That suggests they are not *necessary* for the contract.
  - Either basis must be stated in the privacy notice ([Art 13](https://www.legislation.gov.uk/eur/2016/679/article/13)).

## 3. Unsubscribe requirements

- **Service message:** no statutory unsubscribe requirement under PECR.
  - Under UK GDPR, a Student can object to legitimate-interests processing ([Art 21(1)](https://www.legislation.gov.uk/eur/2016/679/article/21)). In practice the on/off setting handles this.
- **If treated as marketing:**
  - Every email must give a valid address for opt-out requests ([reg 23](https://www.legislation.gov.uk/uksi/2003/2426/regulation/23)), and the soft opt-in needs a simple refusal means in each message.
  - The UK GDPR right to object to direct marketing is absolute: once the Student objects, the data "shall no longer be processed for such purposes" ([Art 21(2)–(3)](https://www.legislation.gov.uk/eur/2016/679/article/21)).
- **Recommendation:** put a one-click link in every Alert that sets the existing single Alert preference to off. Honour it immediately. This meets the stricter (marketing) standard at little cost.

## 4. Link-click tracking

**Law.** Since 5 Feb 2026, PECR reg 6(1) reads: "a person must not store information, or gain access to information stored, in the terminal equipment of a subscriber or user", subject to Schedule A1 (substituted by DUAA 2025 s.112, commenced by SI 2026/82). — [PECR reg 6](https://www.legislation.gov.uk/uksi/2003/2426/regulation/6)

Schedule A1 exceptions:
- consent;
- transmission of a communication;
- strictly necessary for a requested information society service;
- statistical purposes;
- appearance or functionality;
- emergency assistance.

The statistical exception has these conditions:
- the person provides an information society service;
- the sole aim is statistics on how the service is used, so it can be improved;
- the user gets clear and comprehensive information and a simple, free way to object, and does not object.

Source: [PECR Sch A1](https://www.legislation.gov.uk/uksi/2003/2426/schedule/A1)

**ICO guidance (final Storage and Access Technologies guidance, Apr 2026).**
- **Tracking pixels in emails:** "where tracking pixels store information (or gain access to information stored) on a user's device, regulation 6 applies", alongside reg 22. — [ICO: What are storage and access technologies?](https://ico.org.uk/for-organisations/direct-marketing-and-privacy-and-electronic-communications/guidance-on-the-use-of-storage-and-access-technologies/what-are-storage-and-access-technologies/)
- **Link decoration:**
  - Defined as "adding extra information to the URL in a link". Regulation 6 applies where these techniques involve storage of, or access to, information on a device.
  - The ICO's worked example applies reg 6 where the URL ID is *also* stored in a cookie.
  - The page's email newsletter example (`?referrer_source=emailnewsletter`) is not given a reg 6 verdict. — same page
- **Statistical exception:**
  - "about how your service is used, not about who uses it".
  - It "does not allow you to monitor or track individual visitors".
  - Output must be aggregate.
  - Ad view or click tracking is listed as *not* meeting it.
  - Knowing traffic arrived "via an email campaign (ie the referrer URL)" *is* listed as likely to meet it.
  - You must not rely solely on browser settings for objections.
  - Source: [ICO: What are the exceptions?](https://ico.org.uk/for-organisations/direct-marketing-and-privacy-and-electronic-communications/guidance-on-the-use-of-storage-and-access-technologies/what-are-the-exceptions/)
- **Strictly necessary exception:** using link decoration *to authenticate a user* is a likely example. — same page

**What that means for Cadence.**

| Technique | Reg 6? | Notes |
| --- | --- | --- |
| Open-tracking pixel | Yes | Per-Student open tracking is not within the statistical exception, so consent is required. **Avoid.** |
| Redirect or decorated link with a per-Alert token, recorded server-side on click, no cookie set or read for the measurement | **Judgement:** arguably no. The token is in the URL Cadence sent, not read from the device. | UK GDPR still applies: the click is personal data. Use legitimate interests, disclose it in the privacy notice, and set a retention period. |
| Aggregate count only (e.g. `?src=alert`, no per-Student ID) | Within the statistical exception at most | Lowest risk. Measures channel effectiveness, not individual re-engagement. |
| Joining the click to the Student's session cookie to build a per-Student re-engagement metric | Likely yes, and not strictly necessary or statistical | Needs consent. The session cookie is strictly necessary for login, but reusing it for individual tracking is a second purpose (ICO: mixed purposes need consent). |

**Watch:** the ICO advised DSIT (Apr–May 2026) on possible new reg 6 exceptions for online advertising, including measurement. None is law as of this research. — [ICO report for DSIT](https://ico.org.uk/media2/yefdqvk4/20260505-report-for-dsit-on-changes-to-regulation-6-pecr-for-online-advertising.pdf)

**Data residency.** Third-party email providers' click-tracking usually rewrites links through the provider's own domain. That makes the provider a processor of click data and may move data outside the UK. **Inference:** confirm the provider's processing location, or do click tracking first-party.

## 5. Record-keeping

| Record | Source | When |
| --- | --- | --- |
| Alert on/off preference with timestamp of change | Art 21 (honouring objections); reg 22(3)(c) if marketing | Always |
| Evidence of consent (who, when, what they were told, how) | [UK GDPR Art 7(1)](https://www.legislation.gov.uk/eur/2016/679/article/7) | Only if relying on consent (marketing without soft opt-in, pixels, per-Student tracking cookies) |
| Record of processing covering Alerts and click data | [UK GDPR Art 30](https://www.legislation.gov.uk/eur/2016/679/article/30) | Always (the under-250-staff exemption does not apply to regular processing) |
| Legitimate interests assessment for Alerts and click tracking | Art 5(2) accountability (ICO expects an LIA when relying on Art 6(1)(f)) | Always, if using legitimate interests |
| Privacy notice entry: Alerts, click tracking, retention | Art 13 | Always |

PECR itself imposes no record-keeping duty for service messages.

## Open questions

- Does Cadence's signup flow offer a marketing or Alert opt-out at the point the email address is collected? This determines whether the soft opt-in fallback works.
- Will the re-engagement metric be per-Student or aggregate? Per-Student plus any cookie use pushes towards consent.
- Which email provider, where it processes data, and whether its link-rewriting is on by default.
