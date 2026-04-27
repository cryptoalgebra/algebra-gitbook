# How to tweak formula behaviour

Sigmoid coefficients Fee calculation can be tuned by the following parameters:

<pre><code>  struct Configuration {
    uint16 alpha1; // max value of the first sigmoid
    uint16 alpha2; // max value of the second sigmoid
    uint32 beta1; // shift along the x-axis for the first sigmoid
    uint32 beta2; // shift along the x-axis for the second sigmoid
    uint16 gamma1; // horizontal stretch factor for the first sigmoid
    uint16 gamma2; // horizontal stretch factor for the second sigmoid
    uint32 <a data-footnote-ref href="#user-content-fn-1">volumeBeta</a>; // shift along the x-axis for the outer volume-sigmoid
    uint16 <a data-footnote-ref href="#user-content-fn-1">volumeGamma</a>; // horizontal stretch factor the outer volume-sigmoid
    uint16 baseFee; // minimum possible fee
  }
</code></pre>

Adaptive fee function is a combination of several sigmoids. The plot of the standard sigmoid function looks like this:

![Adaptive fee sigmoid](https://lh6.googleusercontent.com/n--z4wld0-5KmjdniTp3ZTNenUFPGC3FqelgAGF-EJjYTH49etPBFl49QOF5lI34YS0fSS-MZNQDSwW9biNtFsW_2CX0_XCzxdDoC1RxSI1yrnGKQ8ilvnnHfo6eCbARNo5-ucLslxV8NaFm1AYhUeuA6BHSSsbPCpL_Z5GId2o06s-phP-Ok8mRGw)

We use a more flexible formula for sigmoids:

![Flexible formula](https://lh4.googleusercontent.com/YnM6CV4_WlDTCWrIYEz_L8NAhHadZYc_A1-BV70_SIgUocb9RRMzo9RSAgN5bZFgD2_EWNzvJg76hzCYQrSAabqg-VxjukwW2k65Ocrp-aaRxA6wns8mV3E1DdK6c8i8bWTfR-mXyJMvPcg6duJPbhYUusyfEGSD3SQ5Gfh4pXSBqFcOH0TXwSMb8g)

So additional params (alpha, gamma, beta) allow us to shift, stretch and modify sigmoids.

![Different options](https://lh4.googleusercontent.com/FAt7pOKBxb1n-VAgMXkNAq8R9mfKThYwjpUCuhbf2FIUaTDO8u7seZvYq_DMGtd4fbgYiAKd4v-CtfURYCj9jgjjq95A2ofhnICWoKJ4t3owP3zZHq9uC-2Tms4kgta96Qkg1O5U_9wli5-2Nd9dzwQXvf6iY4Fa5wXqRDC4nEbMj1odtyFiXrll-w)

Default params:

<pre><code>      2900, // alpha1
      12000, // alpha2
      360, // beta1
      60000, // beta2
      59, // gamma1
      8500, // gamma2
      0, // <a data-footnote-ref href="#user-content-fn-1">volumeBeta</a>
      10, // <a data-footnote-ref href="#user-content-fn-1">volumeGamma</a>
      100 // baseFee
</code></pre>

So

max fee value is: `baseFee` + `alpha1` + `alpha2` min fee value is: `baseFee`

midpoint of the first sigmoid: `beta1` midpoint of the second sigmoid: `beta2`

sigmoid “jump” “starts” around `beta - 6 * gamma`, ends around `beta + 6 * gamma`

![Sigmoid jumps](https://lh3.googleusercontent.com/D0z4h9kG5RqtDOPjLT6fTIpPVYKSIw5v7M64Y0uYd2eELLco11Np4z8PLBo0lFIpFpYCZU52uSOiGnGR1PkjXJz5vRAqmswHxqxe0zsFeFBVwcVzYUj2Y625gzldZKdZxQYJbeT3fnNPkCGUizY-Gy6MgV-_nNRiAecag0kXFgxpTlfWegvjo3fHpQ)

`alpha1`, `alpha2`, `beta1`, `beta2`, `gamma1`, `gamma2` – coefficients for volatility sigmoids. [`volumeBeta`, `volumeGamma`](#user-content-fn-1)[^1] – coefficients for volume sigmoid

[^1]: removed in Integral
