# Monitoring used increment

**Increment On-chain Metrics**

Through [Increment](https://www.covalenthq.com/platform/increment/?utm\_source=notion\&utm\_medium=increment-link\&utm\_campaign=refiner-testnet#/), you are able to view the proofs your Operator address has submitted.

* Instructions

Log into or signup to the [Covalent platform](https://www.covalenthq.com/platform/?utm\_source=notion\&utm\_medium=platform\&utm\_campaign=refiner-testnet).

* Filter the following SQL query so that it returns the results for your Operator address. This will display the number of proofs submitted by your address in 30-minute increments over the past 7 days or you can modify the interval times.

```
SELECT
	count(*) AS proofs,
	hex(tx_sender) AS refiner_operator,
	toStartOfInterval(signed_at, INTERVAL 30 MINUTE) AS interval_start
FROM
	blockchains.all_chains
WHERE
	chain_name = 'moonbeam_moonbase_alpha'
	
	--please enter your "Operator" address in this filter. Remove the '0x' at the beginning
	AND tx_sender = unhex('DacD8481A63f287e93b40f0dA33c93F37xxxxxx')
	
	--topic hash for 'BlockResultProductionProofSubmitted'
	AND topic0 = unhex('8741f5bf89731b15f24deb1e84e2bbd381947f009ee378a2daa15ed8abfb9485')
	
	AND signed_at >= now() - INTERVAL 3 HOUR
GROUP BY
	refiner_operator,
	interval_start
```

> Please change `unhex` with your address without 0x\
> you can modify interval times `- INTERVAL`&#x20;

* Go to [Increment](https://www.covalenthq.com/platform/increment/?utm\_source=notion\&utm\_medium=increment-link\&utm\_campaign=refiner-testnet#/) and select ‘<mark style="color:red;">`Type SQL. Get Charts`</mark>’ click number  3. From here, paste in the above query and filter using your Operator address.

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

* Click <mark style="color:red;">`'On start with and empty query ->'`</mark>

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

* Copy and paste quesry on above

{% hint style="info" %}
Don't forget to change `tx_sender` and modify the `- INTERVAL`
{% endhint %}

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

* Run Query and go to New Chart

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

```
WITH proofs_per_operator AS (
    SELECT
        COUNT(*) AS proofs,
        HEX(tx_sender) AS Operator,
        NOW() AS live_time,
        toStartOfInterval(toDateTime(signed_at), INTERVAL 1 DAY) AS interval_start_raw
    FROM
        blockchains.all_chains
    WHERE
        chain_name = 'moonbeam_moonbase_alpha'
        AND toDate(signed_at) >= '2023-07-14'
        AND topic0 = UNHEX('8741f5bf89731b15f24deb1e84e2bbd381947f009ee378a2daa15ed8abfb9485')
        AND HEX(tx_sender) NOT IN ('CAD9082D5F5E818B1A528A128B6688B8CB484037', '8C0E1EBAC3E4513C5D2338D2BA47CA4BB3483D01')
    GROUP BY
        Operator,
        interval_start_raw
),
total_submissions_per_operator AS (
    SELECT
        Operator,
        SUM(proofs) AS total_submissions_per_operator
    FROM proofs_per_operator
    GROUP BY Operator
),
ranked_operators AS (
    SELECT
        Operator,
        total_submissions_per_operator,
        ROW_NUMBER() OVER (ORDER BY total_submissions_per_operator DESC) AS operator_rank
    FROM total_submissions_per_operator
)
SELECT
    DISTINCT ppo.Operator,
    MAX(ppo.live_time) AS live_time,
    MAX(ppo.proofs) AS proofs,
    rpo.total_submissions_per_operator
FROM proofs_per_operator ppo
JOIN ranked_operators rpo
ON ppo.Operator = rpo.Operator
GROUP BY
    ppo.Operator,
    rpo.total_submissions_per_operator,
    rpo.operator_rank
ORDER BY
    rpo.operator_rank, proofs DESC

```

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>
