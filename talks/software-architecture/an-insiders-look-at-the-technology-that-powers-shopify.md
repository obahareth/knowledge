# [An Insider's Look at the Technology That Powers Shopify](https://www.youtube.com/watch?v=Th7XN__ltyc)

## Quick Summary

Watched this talk because I thought it was more software architecture related but it turned out to be a very general and high level overview on 4 areas of Shopify's technology. Lots of cool Silk Road. economical, and historical references though.

## Open Internet

**Payments**

- Joined W3C in 2016 as a founding member of the [payments working group](https://www.w3.org/Payments/WG/), to standardize payment actions on the internet for 3 years+.
  - Principles:
    - "Do you have a wallet?"
    - "Can you make a payment?"
    - "Let's make it fast."

**3D Models**

- Shopify is working with the [Khronos Group](https://khronos.org) (an open standards body) to advance 3D model formats for the internet.

  

## Security & Privacy

- A merchant's data belongs to the merchant.
- "We have the tools to support you, but you have to use them".
- The biggest threat to a merchant's data is compromised credentials, mainly from phishing emails.
- New identity vault launched: https://accounts.shopfiy.com
  - One account and one setting for everything you do on Shopify.
  - Supports new [Web Authentication API](https://www.w3.org/TR/webauthn/) (WebAuthn, [nice guide here](https://webauthn.guide/)).
    - Allows Shopify servers to interact with secure identity meechanisms on devices (TouchID, Windows Hello, etc.)
- Additional support on data ownership and privacy through new webhooks and APIs 
  - Data request and deletion API.
  - Greater app permission transparency.
- Ranking and monitoring apps on Shopify platform to make sure that their partners are building applications that respect data on behalf of the merchant.
- Processing more than 10 billion events everyday, which totals to 10 petabytes of data.
- Network of 3000+ security engineers who have been paid out more than 1 million USD to find and report vulnerabilities (on test shops).
- Increasing investment on [metafields](https://help.shopify.com/en/manual/products/metafields) on Shopify platform.



## Extensibility

- Providing unique products and experience is a key product to make commerce exciting and enjoyable.
- Guided by the philosophy of The Silk Road.
- 5 years ago Shopify API had lag between announcing APIs and features.
- As of this talk, Shopify is powered by the same REST and GraphQL APIs used by clients. No lag between feature announcements and API announcements anymore.
- New PoS is being built with apps as a core feature built into the experience.
- APIs are features.
- Comitting to building as many APIs they can to keep Shopify the most creative platform for commerce experiences.



## Global Infrastructure

- Scaled from 2 points of presence to 180 in over 80 countries in a single year. Decreasing latency for buyers by 30%  - 50%.
- Launching 4 new regions in next 18 months from this talk's date.
- Requests reach around 5 million per minute.
- Some merchants are selling more than 8000 orders a minute.
- Speed matters more than scale for most merchants.
- Two big performance wins:
  - Upgrade to image delivery service by using WebP (30% smaller and faster).
  - New Liquid renderer that's 7x faster.
