# How To - ACME (Let's Encrypt!) - DNS Manual

<https://www.reddit.com/r/PFSENSE/comments/5v6b0a/how_to_acme_lets_encrypt_dns_manual/>
<https://docs.netgate.com/pfsense/en/latest/packages/acme/index.html>

Just wanted to do a quick write up on what I learned over the weekend, hopefully, it will help someone!
This guide is for using the DNS Manual verification method (the easiest method IMHO) in the ACME
package for PFsense. Generally, it's very easy to use the package, but there is one gotcha with the DNS
Manual method and I'll say it right now, don't hit 'Issue' twice!

## Installation

1. Install the acme package, once that's installed head over to Services -> Acme Certificates.
Create Account Key
1. First head right over to 'Account Keys'. You need to create an account in order for certificates to
issued.
2. Click Add. Give it name you can pick any you want, I did domain-tld-acme.
3. Select the Production Acme server (I wouldn't pick the staging CA for any reason unless you are never
going to use the cert in production, I'll explain why later on).
4. Hit that big 'Create new account key' button to generate a new PKI key pair. Then hit 'Register acme
account key'.
5. Hit that small Save button now.

## Create Certificate Profile

1. Head over to 'Certificates' and hit 'Add'.
2. Give it a name, I always do domain-tld-prod, but do whatever you like.
3. Select your Acme Account to the account you just created.
4. Pick your key size. I do ec-384, but that's just me. I would do that or 4096. The only reason I would do
smaller is if you have thousands of SSL sessions and the key size really has an impact on
performance for you.
5. Now you can put in the domains you need the cert for. Put the Domain name in (www.contoso.com)
and select the 'DNS Manual' method (this is the verification for the domain to ensure that you are
authoritative for that domain).
6. Keep adding all the domains you need, you can up to 100 domains per cert I believe.
7. Add what actions you need into the 'Actions List'. The examples right there work for the Webgui or HA
Proxy. (This is required for when you get a new cert, what ever process was using the old cert needs
to restart to use the new one)
8. Leave DNS-Sleep and Cert renewal after to the defaults.
9. Hit Save!

## Issuing and Verification

1. Here's where the fun starts.
2. Hit "Issue" (this is the one and only time you'll hit 'Issue'. Don't hit it again!). Wait for the response.
3. When it comes back it will say the follow, copy and paste the message into your editor: Add the
following TXT record: Domain: '_acme-challenge.www.contoso.com' TXT value:
'LONGKEYVALUEYAY123456789'
4. Okay, now go to create that TXT record and all the other records (you'll have one per domain name in
the cert).
5. If you hit Issue again now, you'll get new verification keys for the TXT records, DON'T HIT ISSUE
AGAIN! Now this is also why I say you shouldn't use the Staging CA. If you did this on the Staging CA
and then said, cool, I want a Production cert now, You'll have to recreate all those TXT records you
created. (No problem if one domain, Yes problem if 50+ domains :) )
6. Instead, once those TXT records are created, hit 'Renew'.
7. Assuming that you made those records properly, acme will verify those TXT values and you'll get a
pretty little cert back from Let's Encrypt!.
8. If the verification failed, it will say what domain is wrong. The fastest way to update a TXT is to delete
it and create it again (opposed to just simply updating it). Then hit 'Renew' again.
9. That cert is placed into Pfsense's Cert Manager and can be used anywhere or even downloaded.
10. You can edit the cert profile any time you want (to add actions). If you add a new domain, save it then
hit Renew, I believe. I'll have to double-check that and then update this post if I'm right or wrong.


