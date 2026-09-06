# Deploying kevinsotka.com

First-time setup, start to finish. After this, deploying is just `git push`.

---

## 1. Push

The repository already exists at https://github.com/kevin-sotka/kevinsotka.com. It is public
and empty, this folder is already a git repository with everything committed on `main`, and
`origin` is already pointed at it.

One command left:

```bash
cd ~/Meatbag_Labs/kevinsotka.com/site
git push -u origin main
```

If git asks for a password, it wants a personal access token rather than your account
password. The easier route is the GitHub CLI, which handles auth in a browser window:

```bash
brew install gh
gh auth login
git push -u origin main
```

Public is the right choice for this repository. It holds one marketing page and no secrets.
The strategy documents, evidence library, and archive live one folder up and are deliberately
outside this repository.

---

## 2. Turn on GitHub Pages

In the repository, go to **Settings** then **Pages**.

- Under **Source**, choose **Deploy from a branch**.
- Set the branch to `main` and the folder to `/ (root)`.
- Save.

Within a minute or two the site is live at `https://kevin-sotka.github.io/kevinsotka.com/`.
Confirm it loads before touching DNS. It is much easier to debug a broken page on that URL
than through a half-propagated domain.

---

## 3. Point the domain at GitHub Pages, in Namecheap

Log in to Namecheap, open **Domain List**, click **Manage** next to `kevinsotka.com`, then
open the **Advanced DNS** tab.

### Delete what is already there

Namecheap adds two records to every new domain. Both will break this. Remove them.

- The **CNAME Record** for host `www` pointing to `parkingpage.namecheap.com`.
- The **URL Redirect Record** for host `@`.

### Add these five records

| Type | Host | Value | TTL |
|------|------|-------|-----|
| A Record | `@` | `185.199.108.153` | Automatic |
| A Record | `@` | `185.199.109.153` | Automatic |
| A Record | `@` | `185.199.110.153` | Automatic |
| A Record | `@` | `185.199.111.153` | Automatic |
| CNAME Record | `www` | `kevin-sotka.github.io.` | Automatic |

All four A records are required. They are GitHub's load balancer addresses and any one of
them alone will give you an unreliable site.

The CNAME value ends with a period. Namecheap usually adds it for you, and it is correct
either way.

**Optional, for IPv6 visitors.** Add these four AAAA records with host `@`:

```
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```

Make sure the **Nameservers** setting on the Domain tab is **Namecheap BasicDNS**. If it
points somewhere else, the Advanced DNS records here are being ignored.

---

## 4. Set the custom domain in GitHub

Back in **Settings** then **Pages**, under **Custom domain**, enter `kevinsotka.com` and
save. This writes a `CNAME` file to the repository, which already exists here, so nothing
should change.

GitHub then checks DNS. This can take anywhere from a few minutes to a few hours, and the
page may show a DNS error the whole time it is waiting. That is normal.

Once the check passes, tick **Enforce HTTPS**. That checkbox stays greyed out until GitHub
has finished issuing the certificate, which is usually under an hour but is occasionally
slower. Do not skip it. An unencrypted site gets a browser warning that undoes the
impression the page is trying to make.

---

## 5. Verify

```bash
dig +short kevinsotka.com          # should list the four 185.199.x.153 addresses
dig +short www.kevinsotka.com      # should resolve through kevin-sotka.github.io
```

Then load `https://kevinsotka.com` in a private window and check that the padlock is there
and that `www.kevinsotka.com` redirects to it.

---

## 6. The email address

`hello@kevinsotka.com` is already live, running on Namecheap's free email forwarding. Incoming
mail arrives in the inbox it forwards to, so the address on the page works today.

One thing to know about it: forwarding is receive-only by design. Replies go out from whichever
account you answer with, not from `hello@kevinsotka.com`. If that bothers you, a real mailbox
fixes it. Namecheap's Private Email Launch plan supports IMAP and SMTP, which lets you add the
address to Gmail as a send-as identity and reply from it while still working out of the inbox you
already check. Check their current pricing, and note there is a free trial.

---

## Routine deploys, after all of the above

```bash
git add .
git commit -m "Describe the change"
git push
```

Live in a minute or two.

---

## If something goes wrong

**The page loads but has no styling.** Check that `.nojekyll` is still in the repository.

**GitHub says the domain is not properly configured.** Usually DNS has not propagated yet.
Confirm with `dig +short kevinsotka.com` that the four A records are answering, then wait.
If they are not answering, the old Namecheap URL Redirect record is probably still present.

**Enforce HTTPS is greyed out.** The certificate has not been issued yet. Wait, then reload
the settings page. If it is still greyed out after a day, remove the custom domain, save,
re-add it, and save again.

**The site shows an old version.** GitHub Pages caches. Hard reload, or check the Actions
tab in the repository to confirm the deploy actually finished.
