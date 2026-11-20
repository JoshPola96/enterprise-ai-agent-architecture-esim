<!-- docs/test-log/PROVENANCE.md -->
[&larr; Back to the record](../../README.md#from-the-test-logs)

# Provenance of the Test-Log Exhibits

*How every screenshot, voice note and video in this case study was made from the original record, and how to check each one.*

---

## What is bound, and how

- **The two chat exports**, Telegram's and WhatsApp's, are hashed in [`SHA256SUMS`](SHA256SUMS). Every message the paper cites, by Telegram id (`#4810`) or WhatsApp line, and every voice note, is inside them.
- **Every file in [`audio/`](audio)** is an original voice note, byte for byte, hashed on its own.
- **Every redacted screenshot** was made from an original whose hash is in `SHA256SUMS`, by the recipe below. The originals stay private because the boxes cover codes, contact details and eSIM identifiers; their hashes let them be checked later without revealing what is under the boxes.

In this folder, `sha256sum -c --ignore-missing SHA256SUMS` checks every hashed file that is present: today, the audio and the verification page.

## The redaction recipe

Open the original and convert it to RGB. Fill each listed rectangle solid black; coordinates are `(x0, y0, x1, y1)` in the original's pixels. Crop to the listed rectangle, if there is one. Stack the parts top to bottom on a `#0e1621` background, then save as PNG (optimised) or JPEG at quality 92, with no metadata.

Run on the originals, the recipe reproduces every published image below **pixel for pixel**. That was checked for all 21 on 21 November 2026, together with each original's hash.

## Exhibit by exhibit

| Published | Original in `SHA256SUMS` | Crop | Boxes | Shows |
|---|---|---|---|---|
| [`split_payment_chat.png`](split_payment_chat.png) | `originals/Screenshot 2026-11-16 181118.png` | none | `(126, 85, 190, 156)` | Telegram, 17 July 2026, #4588–4593: [the split that half-ran](../05-commerce.md#from-the-test-log-the-money-and-the-words) |
| [`phantom_purchase_chat.png`](phantom_purchase_chat.png) | `originals/Screenshot 2026-11-16 180623.png` | none | `(318, 748, 378, 768)` | Telegram, 24 July 2026, #4801–4812: [two confirmations, one debit](../05-commerce.md#from-the-test-log-the-money-and-the-words) |
| [`purchase_confirmed_chat.png`](purchase_confirmed_chat.png) | `originals/Screenshot 2026-11-16 180821.png` | none | `(225, 738, 294, 758)` | Telegram, 2 August 2026, #5013–5019: the clean purchase |
| [`balance_chat.png`](balance_chat.png) | `originals/Screenshot 2026-11-16 181610.png` | none | none | Telegram, 3 August 2026, #5031–5040: the balance five hours later |
| [`human_gate_chat.png`](human_gate_chat.png) | `originals/Screenshot 2026-11-16 180930.png` | none | `(40, 159, 412, 179)`, `(16, 178, 200, 197)`, `(534, 384, 640, 405)` | Telegram, 3 August 2026, #5020–5028: [the human gate](../07-security.md#5-the-human-gate) |
| [`manual_verification_chat.png`](manual_verification_chat.png) | `originals/Screenshot 2026-11-16 181807.png`<br>`originals/Screenshot 2026-11-16 181851.png` | `(0, 312, 684, 826)`<br>`(0, 0, 674, 805)` | `(58, 743, 210, 762)`, `(58, 797, 210, 816)`, `(246, 525, 670, 707)` labelled “dashboard image removed”<br>`(56, 77, 206, 96)`, `(56, 191, 206, 210)`, `(56, 311, 206, 330)`, `(56, 549, 206, 568)`, `(56, 603, 206, 622)` | Telegram, 24–25 July 2026, #4845–4864: [flagged by its own agent](../07-security.md#on-the-record-the-agent-flagged-me) |
| [`otp_repeated_chat.jpg`](otp_repeated_chat.jpg) | `originals/wa_L1022-1039.jpeg`<br>`originals/wa_L1038-1050.jpeg` | `(0, 240, 720, 520)`<br>`(0, 172, 720, 792)` | `(312, 381, 404, 407)`<br>`(482, 356, 574, 388)`, `(59, 641, 150, 674)` | WhatsApp, 23 May 2026, line 1025, and 26 May 2026, lines 1039 and 1041: [the code the agent repeated](../04-identity.md#case-study-the-verification-code-the-agent-repeated) |
| [`human_gate_first_night_chat.jpg`](human_gate_first_night_chat.jpg) | `originals/wa_L1038-1050.jpeg`<br>`originals/wa_L1045-1059.jpeg` | `(0, 805, 720, 1208)`<br>`(0, 200, 720, 1146)` | `(44, 1164, 358, 1197)`<br>`(44, 560, 337, 592)`, `(515, 1047, 586, 1078)`, `(44, 1082, 570, 1113)` | WhatsApp, 26–27 May 2026, lines 1044–1057: the gate's first night |
| [`human_gate_whatsapp_chat.jpg`](human_gate_whatsapp_chat.jpg) | `originals/wa_L1083-1102.jpeg` | `(0, 190, 720, 1222)` | `(515, 604, 566, 634)`, `(44, 638, 562, 670)` | WhatsApp, 27 May 2026, lines 1088–1094: the held message answered |
| [`otp_template_chat.jpg`](otp_template_chat.jpg) | `originals/wa_L1134-1148.jpeg` | `(0, 798, 720, 1412)` | `(312, 930, 394, 960)`, `(43, 1164, 125, 1192)` | WhatsApp, 28–29 May 2026, lines 1143–1148: the authentication template |
| [`settings_walkthrough_20jul.png`](settings_walkthrough_20jul.png) | `originals/tg_4678-4679.png`<br>`originals/tg_4681-4683.png` | `(0, 6, 646, 772)`<br>`(0, 14, 671, 780)` | none<br>none | Telegram, 20 July 2026, #4678–4683: [phone settings read from screenshots](../06-voice.md#replies-in-the-right-mode) |
| [`settings_walkthrough_21-23jul.png`](settings_walkthrough_21-23jul.png) | `originals/tg_4698-4699.png`<br>`originals/tg_4733-4736.png`<br>`originals/tg_4737-4738.png` | `(0, 10, 687, 600)`<br>`(0, 0, 643, 605)`<br>`(0, 12, 657, 650)` | `(482, 121, 527, 132)`, `(575, 121, 620, 132)`<br>none<br>none | Telegram, 21 July 2026, #4698–4699, and 23 July 2026, #4733–4738 |
| [`broadcast_misread_chat.jpg`](broadcast_misread_chat.jpg) | `originals/wa_L934-945.jpeg` | `(0, 488, 720, 1268)` | `(492, 504, 582, 538)` | WhatsApp, 20 May 2026, lines 936–944: [the broadcast taken for my own words](../05-commerce.md#message-delivery--localisation) |
| [`dev_claim_28dec_chat.jpg`](dev_claim_28dec_chat.jpg) | `originals/wa_L247-258.jpeg` | `(0, 236, 720, 800)` | none | WhatsApp, 28 December 2025, lines 248–257: ["I'm your dev"](../07-security.md#on-the-record-im-your-dev), acted on |
| [`dev_claim_13mar_chat.jpg`](dev_claim_13mar_chat.jpg) | `originals/wa_L624-642.jpeg`<br>`originals/wa_L643-652.jpeg` | `(0, 722, 720, 1400)`<br>`(0, 198, 720, 1222)` | none<br>none | WhatsApp, 13 March 2026, lines 637–650: the same claim, refused |
| [`voice_login_ss.jpeg`](../images/voice_login_ss.jpeg) | `originals/voice_login_ss.jpeg` | none | `(436, 238, 698, 260)`, `(303, 294, 466, 315)` | Telegram, 18 July 2026, #4646–4654: the superseded design over voice |
| [`latest_login_flow_ss_2.jpeg`](../images/latest_login_flow_ss_2.jpeg) | `originals/latest_login_flow_ss_2.jpeg` | none | `(274, 385, 429, 406)`, `(34, 406, 250, 432)`, `(369, 459, 435, 480)` | WhatsApp, 30 August 2026, lines 1757–1771: the login relay re-issuing an expired link |
| [`broadcast_system_ss.jpeg`](../images/broadcast_system_ss.jpeg) | `originals/broadcast_system_ss.jpeg` | none | `(369, 152, 435, 173)` | WhatsApp, 16–17 June 2026, lines 1593–1606: a broadcast through the notification path |
| [`voice_browsing_ss.jpeg`](../images/voice_browsing_ss.jpeg) | `originals/voice_browsing_ss.jpeg` | none | `(277, 4, 420, 28)` | Browsing by voice: spoken questions, a spoken reply, pricing dropped into text |
| [`latest_login_flow_ss1.jpeg`](../images/latest_login_flow_ss1.jpeg) | `originals/latest_login_flow_ss1.jpeg` | none | `(248, 184, 521, 202)`, `(248, 356, 519, 374)`, `(866, 402, 918, 424)`, `(85, 644, 131, 701)`, `(132, 684, 309, 701)` | The login relay on the production domain, on a second test account |
| [`outdated_password_login_flow_automatic_trial_provisioned_ss.jpeg`](../images/outdated_password_login_flow_automatic_trial_provisioned_ss.jpeg) | `originals/outdated_password_login_flow_automatic_trial_provisioned_ss.jpeg` | none | `(74, 337, 254, 356)`, `(36, 455, 152, 478)`, `(36, 518, 221, 537)`, `(36, 576, 83, 596)`, `(36, 692, 322, 728)` | The superseded credentials-in-chat design, on a test identity |

The other images in [`../images`](../images) carry no boxes and were published as they were captured.

## The videos

Each video in this folder is a still card, laid out from the messages' own text, followed by the original voice notes in order, with 0.8 seconds of silence between them. The audio is decoded and re-encoded for the video, never filtered or normalised. These are the files embedded in the chapters.

| Video | Messages | Originals |
|---|---|---|
| [`phantom_purchase.mp4`](phantom_purchase.mp4) | Telegram, 24 July 2026, #4808 and #4810 | [`tg-4810.ogg`](audio/tg-4810.ogg); #4808 in the hashed Telegram export |
| [`stt_before_after.mp4`](stt_before_after.mp4) | Telegram, 20 July and 2 August 2026, #4683, #4684, #5012, #5013 | [`tg-4683.ogg`](audio/tg-4683.ogg), [`tg-4684.wav`](audio/tg-4684.wav), [`tg-5012.ogg`](audio/tg-5012.ogg), [`tg-5013.ogg`](audio/tg-5013.ogg) |
| [`tts_emoji_names.mp4`](tts_emoji_names.mp4) | Telegram #4868, 26 July 2026; WhatsApp line 1687, 29 July 2026; Telegram #5028, 3 August 2026 | [`tg-4868.ogg`](audio/tg-4868.ogg), [`wa-1687.opus`](audio/wa-1687.opus), [`tg-5028.ogg`](audio/tg-5028.ogg) |
| [`hindi_english_voice.mp4`](hindi_english_voice.mp4) | Telegram, 3 August 2026, #5034, #5035, #5040 | [`tg-5034.ogg`](audio/tg-5034.ogg), [`tg-5035.ogg`](audio/tg-5035.ogg), [`tg-5040.ogg`](audio/tg-5040.ogg) |
| [`malayalam_voice.mp4`](malayalam_voice.mp4) | WhatsApp, 30 August 2026, lines 1805–1808, 1815 and 1816 | [`wa-1808.opus`](audio/wa-1808.opus), [`wa-1816.opus`](audio/wa-1816.opus); the rest in the hashed WhatsApp export |

---

[&larr; Back to the record](../../README.md#from-the-test-logs)
