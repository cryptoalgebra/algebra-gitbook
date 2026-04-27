---
icon: integral
---

# Overview of Algebra Integral

Until now, DEX infrastructure has followed a monolithic design — immutable codebases, where even minor upgrades require costly and disruptive liquidity migrations. As DeFi evolves, these rigid systems struggle to keep pace, leading to outdated features, security risks, and inefficient capital deployment.

The new version of Algebra, Algebra Integral, introduces a higher level of flexibility for partners and users of the protocol, keeping the Concentrated Liquidity efficiency at the core of its design. It serves as an engine for DEXs, finding the perfect balance between gas costs and functionality. The modular architecture allows for the inclusion or exclusion of specific modules, enabling seamless management of components without the need for liquidity migration.

* The new modular structure allows for optimizing efficiency via **disabling, switching on, or upgrading of individual plugins** **without wasting gas**. This means that plugin updates can be performed without the necessity of withdrawing liquidity from the platform, reducing disruptions for users.
* Transactional gas costs can also be significantly reduced thanks to the implementation of the modular architecture.
* With Algebra Integral, the functionality of decentralized exchanges expands, providing DEX teams with **the opportunity to enhance their platforms** by integrating new implementations easily. This empowers them **to adapt to the evolving needs of the market** and offer a more comprehensive and competitive trading experience.
* By moving all secondary functionalities into plugins, **the core of the protocol becomes smaller and simpler**. This results in **easier testing, auditing, and verification** processes. Ultimately, it enhances the security of a critical component for DEX operations.

Thus, the modular structure enhances the security of decentralized exchanges, providing a safer environment for LPs, farmers, traders, and other participants. It ensures that their assets and transactions are better protected.

Learn more:

{% content-ref url="how-it-works-core-+-plugins.md" %}
[how-it-works-core-+-plugins.md](how-it-works-core-+-plugins.md)
{% endcontent-ref %}

{% content-ref url="v3-vs.-v4-key-differences.md" %}
[v3-vs.-v4-key-differences.md](v3-vs.-v4-key-differences.md)
{% endcontent-ref %}

{% content-ref url="integral-vs.-uniswap-v4-key-differences.md" %}
[integral-vs.-uniswap-v4-key-differences.md](integral-vs.-uniswap-v4-key-differences.md)
{% endcontent-ref %}

{% content-ref url="../benefits-of-modular-architecture/" %}
[benefits-of-modular-architecture](../benefits-of-modular-architecture/)
{% endcontent-ref %}
