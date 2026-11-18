# Low latency VPS Asia: which city serves your users, how to test it before paying, and what one trader's 80 ms problem turned out to be (full plan table)

If 60% of your users are in Indonesia, a Singapore server is the wrong purchase and a Jakarta one is the right one. If your audience is split between the Philippines and Vietnam, Singapore sits between them at roughly 60 ms to Manila and 70 ms to Ho Chi Minh City. Hong Kong and Tokyo earn their place only when the users are in Hong Kong, Taiwan, Japan or Korea, or when you need CN2 routes into mainland China.

LightLayer's Asian carts cover Hong Kong, Taiwan, Japan, Thailand, Singapore and the Philippines. Entry pricing runs $4.00/month in Los Angeles, $5.00/month in Hong Kong, and $24.99/year for a 1 core / 1 GB / 50 GB SSD box in Manila or Taiwan. That shape suits a deployment of several small servers, one near each user cluster, better than one larger server in Singapore.

What follows covers the three things that decide whether your Asia VPS feels fast: the city you pick, the measurements you take before paying, and the share of your latency that no datacenter can fix.

> **Quick pick**
> - Users in the Philippines, Thailand or Vietnam: LightLayer's Manila cart, 1 core / 1 GB / 50 GB SSD at **$24.99/year**.
> - Users in Hong Kong, Taiwan or mainland China: the Hong Kong cart, where the CN2 GIA routes out of Hong Kong matter more than the CPU does.
> - Users in Japan or Korea: Tokyo.
> - 👉 [Check the current LightLayer Asia carts and entry prices](https://account.lightlayer.net/?affid=74)

## What LightLayer actually sells

LightLayer prices by city rather than through one flat global tier list, and that is the clearest signal about who the product is for. The same 1 core / 1 GB / 50 GB shape costs $4.00/month in Los Angeles, $5.00/month in Hong Kong, and $24.99/year in Manila or Taiwan.

| Plan & city | CPU / RAM | Storage | Bandwidth | Price | Order |
| --- | --- | --- | --- | --- | --- |
| Startup, Los Angeles | 1 core / 1 GB | 50 GB SSD | 100 Mbps unmetered | $4.00/mo | [Order](https://account.lightlayer.net/index.php?/cart/los-angeles/&affid=74) |
| Startup, Hong Kong | 1 core / 1 GB | 50 GB SSD | 1 TB | $5.00/mo | [Order](https://account.lightlayer.net/index.php?/cart/hong-kong/&affid=74) |
| Startup, Manila (annual) | 1 core / 1 GB | 50 GB SSD | not listed | $24.99/yr | [Order](https://account.lightlayer.net/index.php?/cart/manila/&affid=74) |
| Startup, Taiwan (annual) | 1 core / 1 GB | 50 GB SSD | not listed | $24.99/yr | [Order](https://account.lightlayer.net/index.php?/cart/taiwan/&affid=74) |
| Pay As You Go | 2+ cores / 2 GB+ | scalable | 200 Mbps+ | from $5.60/mo | [Configure](https://account.lightlayer.net/?affid=74) |
| Mid-Tier, Hong Kong | 4 cores / 4 GB | 80 GB SSD | 200 Mbps unmetered | $11–18/mo | [Order](https://account.lightlayer.net/index.php?/cart/hong-kong/&affid=74) |
| Business | 32 cores / 32 GB | scalable | 200 Mbps unmetered | $53.60/mo | [Order](https://account.lightlayer.net/?affid=74) |

A few details sit outside the table. Annual billing in Los Angeles runs $29.99/year on the global route, $39.99 on standard and $49.99 on premium, so the network path is priced separately from the hardware. Every plan runs on KVM with AMD EPYC hosts, and storage is SSD or NVMe depending on the city. Each city has its own cart with more configurations than the four recurring tiers above; check the spec and the renewal price on the order page before you pay.

The refund policy matters more than the price for anyone comparing cities. LightLayer refunds provider-side faults only. Regular-priced and promotional plans are not refundable for other reasons, including the common case of "the latency in Jakarta was not what I hoped". Buy monthly while you are still deciding, and treat the annual price as a reward for a decision you have already made.

👉 [Open the Manila cart to confirm the $24.99/year entry plan](https://account.lightlayer.net/index.php?/cart/manila/&affid=74)

## One datacenter cannot cover Asia

Light in fibre travels at about 200,000 km/s, so every 1,000 km between the server and a user adds roughly 5 ms one way, about 10 ms round trip, before any routing overhead. That is a physical floor. A single Singapore machine serving the whole of Asia-Pacific puts 100 ms or more in front of the furthest users before your application runs a line of code, and a page assembled from a dozen requests pays that cost a dozen times.

The numbers below are published figures from a Singapore-based host's looking glass and from a cloud provider's technical note, not independent measurements. Use them to shortlist a city, then verify with your own traceroute.

| From Singapore to | RTT |
| --- | --- |
| Singapore (local) | <2 ms |
| Hong Kong | ~30 ms |
| Jakarta | ~30 ms |
| Bangkok | ~45 ms |
| Mumbai | ~55 ms |
| Manila | ~60 ms |
| Tokyo | ~65 ms |
| Ho Chi Minh City | ~70 ms |
| Sydney | ~95 ms |
| Los Angeles | ~165 ms |
| Frankfurt | ~170 ms |
| Perth | ~180 ms |

The useful reading is not the absolute value but the shape. Everything within 1,500 km of Singapore lands inside 70 ms, which is why one Southeast Asian hub works for a spread-out audience. Perth is the outlier at 180 ms, and no amount of tuning fixes that. Sydney or Melbourne hosting does.

## Match the city to where the users actually are

Start with the concentration, not the map. If 60% of your traffic is Indonesian, Jakarta is the answer and Singapore is a compromise you will pay for on every request. For a Philippine and Vietnamese audience with no single dominant market, Singapore wins because it sits between both, at 60 ms and 70 ms respectively.

Hong Kong and Tokyo get chosen because they are "in Asia". Measured from Singapore, both add 30 to 60 ms to Manila and Jakarta. That is a real downgrade for a Southeast Asian user base, and it is the most common placement mistake in this category.

| Your users | City to pick | Why |
| --- | --- | --- |
| Indonesia-dominant | Jakarta | Singapore's 30 ms is a floor, not a target |
| Philippines + Vietnam mixed | Singapore | 60 ms and 70 ms from one box |
| Hong Kong, Taiwan, mainland China | Hong Kong | CN2 GIA routes into China beat generic BGP |
| Japan, Korea | Tokyo | 65 ms from Singapore means Tokyo is closer for them |
| Thailand-heavy | Bangkok or Singapore | 45 ms; the local cart wins on the last hop |
| Australia | Sydney or Melbourne | Perth is 180 ms from Singapore |

For a multi-cluster audience, the rule is one region per cluster rather than one large region for everything. A user in Manila does not benefit from the disk you bought for Jakarta.

👉 [Look at the Hong Kong cart if your users are in Hong Kong or mainland China](https://account.lightlayer.net/index.php?/cart/hong-kong/&affid=74)

## The 30-minute test that settles it before you pay

Test from a machine on the same ISP as your users, not from your office and not from a monitoring node in Frankfurt. Run it between 7 pm and 11 pm local time, when the access network is busy, and read the 95th percentile rather than the best ping you can find at 3 am. A provider can advertise low latency and still hand you 150 ms with 40 ms of jitter if the route crosses three transit networks.

1. **Ping the test IP from a user-side machine.** Any provider that publishes a test IP or a looking glass will do; if the cart page does not list one, ask support before ordering.
2. **Run `mtr` to the same IP.** This tells you which hop adds the delay. A route that goes Tokyo to Osaka and back to Tokyo is a routing problem, not a distance problem.
3. **Measure `curl` time to first byte** from the user location, which combines network and application into the number your users actually experience.
4. **Compare peak against off-peak**, and repeat from two candidate cities on the same evening.

The thresholds worth holding a provider to: under 80 ms round trip to the user cluster, jitter under 10 ms, and packet loss under 0.1%. Loss above that turns retransmissions into latency you cannot tune away. Direct peering with the major local ISPs is what keeps those numbers stable at 9 pm.

## When the server is not the problem

A trader running a bot on a Tokyo VPS posted his measurements in r/VPS in August 2025. During Japanese peak hours, 8 pm to 1 am, his average latency went from 80 ms to 160–180 ms. Moving to a dedicated CPU removed the spikes above 250 ms but left the average where it was. He had already tested AWS, Azure, Google Cloud, Vultr, DigitalOcean, Linode and DDPS, and the best he could get was around 80 ms.

His telemetry over 513 measurements breaks the 80.20 ms average into three parts: 60.90 ms from client to server, 0.41 ms of server processing at the exchange, and 18.90 ms back. The server itself was using half a percent of the round trip. The outbound path was three times slower than the return path, which pointed at routing rather than hardware.

His peak-hour route went Tokyo to Osaka and back to Tokyo. Placing the server in Osaka improved things slightly. The actual fix was in his own code: replacing the `requests` library with `aiohttp` dropped latency to around 30 ms.

Treat this as one user's account, not a universal law. The sequence is what makes it worth reading. He measured the breakdown before migrating, found the route loop before buying another server, and found the client library last. Most people do those three steps in exactly the wrong order, buy a fourth VPS, and conclude that Asia is just slow.

## Where LightLayer fits

The Asian coverage is the reason to look at it: Hong Kong, Taiwan, Japan, Thailand, Singapore and the Philippines. That is the right set for a Southeast Asian audience plus an East Asian one, and it lets you place a cheap 1 GB box per cluster instead of routing everyone through Singapore. The Hong Kong CN2 GIA routes are the ones reviewers single out, and they matter if any of your traffic goes to mainland China.

The entry prices support the multi-city approach. At $24.99/year, a Manila box costs less than most providers charge for a month of a similar spec, so running Manila and Singapore side by side and measuring both is affordable.

Where it does not fit: there is no Indonesian cart, so an Indonesia-heavy audience still needs a Jakarta provider. There is no managed support tier to fall back on. The refund policy is provider-fault only, so you cannot test a city for two weeks and return it. And if you need Equinix TY3-grade financial colocation in Tokyo, with the exchange cross-connects that go with it, this is not that product. For those cases, Tokyo has providers colocated in TY3 and TY8, and AWS operates its own Tokyo region, but expect to pay for the address rather than the CPU.

## How the alternatives compare

OneAsiaHost runs a single Singapore location from $10/month for 2 GB RAM, 1 vCPU, 60 GB SSD and 5 TB of traffic on a 10 Gbps uplink, KVM-isolated and unmanaged, with a 99.5% SLA. It publishes a looking glass, which makes the pre-purchase test easy. That is the sensible pick if Singapore is your only city and you want one provider with a long operating history.

Tencent Cloud CVM advertises 22 regions and 64 availability zones, free private-network traffic within a region, up to 160 Gbps of internal bandwidth on its SA5 instances, and Cloud Connect Network for linking regions. The vendor's own figures also claim 99.975% single-instance and 99.995% availability-zone availability. Useful if you want one contract covering several countries, though the far-east and mainland regions behave differently from the Southeast Asian ones.

Kamatera prices its Asian regions the same as everywhere else, bills hourly or monthly without a minimum contract, and offers a 30-day free trial. The trial is the practical advantage for anyone who wants to test three cities before committing.

## Pick by situation

- One small site, bot or API serving Manila or Taipei: the 1 core / 1 GB annual plan at $24.99/year covers it.
- Hong Kong users or mainland China traffic: the Hong Kong mid-tier at $11–18/month, because the route matters more than the cores.
- Audience spread across three or more countries: monthly billing on two small boxes in two cities, measured for a month, then commit to annual on the winner.
- Exchange-adjacent trading in Tokyo: neither LightLayer nor a generic cloud VPS; look at TY3-colocated providers first.

Two things to confirm before you enter card details. First, that the cart's city is the city your users are in, not the nearest one that happens to be listed as "Asia". Second, the renewal price and the refund terms for the exact plan in that cart, since promotional pricing and provider-fault-only refunds are both standard here.

👉 [Open the LightLayer cart and check the city list and current prices yourself](https://account.lightlayer.net/?affid=74)
