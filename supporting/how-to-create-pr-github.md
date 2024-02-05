# How to create PR Github

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

Fork [https://github.com/karnotxyz/avail-campaign-listing](https://github.com/karnotxyz/avail-campaign-listing)

Go to setting and create new token classic\


<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

Check repo\


<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Clone repo at your github, follow this command

```
https://github.com/<YOUR_GITHUB_NAME>/avail-campaign-listing.git
```

example

```
https://github.com/ruangnode/avail-campaign-listing.git
```

Go to directory app chain.

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

Go to browser and search [https://www.uuidgenerator.net/version4](https://www.uuidgenerator.net/version4)

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

create json file in app\_chains directory, example :&#x20;

```
nano 19707389-217d-4bfe-b406-7a466c3af14b.json
```

```
{
  "name": "YOUR_NAME",
  "logo": "https://i.imgur.com/YOUR_LOGO",
  "rpc_url": "http://<YOUR_IP_SERVER>:9944",
  "explorer_url": "http://<YOUR_IP_SERVER>:4000",
  "metrics_endpoint": "http://<YOUR_IP_SERVER>:9615/metrics",
  "id": "YOUR_UUID_GENERATOR"
}
```

Commit to github

```
git add .
git commit -m "✨ Adding <YOUR_KARNOT_NAME>"
git push -u origin main
```

{% hint style="info" %}
Username for 'https://github.com': &#x20;

Password for 'https://ruangnode@github.com':\
\
username, input with username github

Password, input with your token classic
{% endhint %}

How to merge your githun and github official for listing app.\
Go to your github, click contribute and Open pull request.

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Create pull and input and add a little with "✨ Adding \<YOUR\_MADARA\_NAME>" and click create pull request. Wait until merge with repo official.\
\
DONE

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>
