# resolvers

[![updated](https://img.shields.io/github/last-commit/threatify/resolvers?label=updated&style=flat-square)](https://github.com/threatify/resolvers/commits/main)
[![resolvers](https://img.shields.io/badge/resolvers-17,877-2ea44f?style=flat-square)](resolvers.txt)
[![license](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](LICENSE)

Public DNS resolvers that answer correctly, re-validated every 6 hours.
**17,877** resolvers passed in the run of 2026-11-11 13:54 UTC.

Made for subdomain enumeration with [puredns](https://github.com/d3mondev/puredns),
[massdns](https://github.com/blechschmidt/massdns), [shuffledns](https://github.com/projectdiscovery/shuffledns),
[dnsx](https://github.com/projectdiscovery/dnsx) and anything else that takes a resolver list.

## Usage

```sh
curl -sO https://raw.githubusercontent.com/threatify/resolvers/main/resolvers.txt
curl -sO https://raw.githubusercontent.com/threatify/resolvers/main/resolvers-trusted.txt

puredns bruteforce wordlist.txt example.com -r resolvers.txt --resolvers-trusted resolvers-trusted.txt
shuffledns -d example.com -w wordlist.txt -r resolvers.txt -mode bruteforce
massdns -r resolvers.txt -t A -o S -w results.txt names.txt
```

## Files

| File | Contents |
|------|----------|
| [`resolvers.txt`](resolvers.txt) | Every resolver that passed the latest run. One IPv4 address per line, sorted. |
| [`resolvers-trusted.txt`](resolvers-trusted.txt) | Large anycast resolvers that do not filter answers (Cloudflare, Google, Quad9 unsecured, Control D unfiltered, Hurricane Electric), for confirming results. |

Both files are replaced in full on every update.

## What "validated" means

Each resolver is checked in two separate rounds, with different names each time, and has to
get every answer right:

- **Correct answers.** A name with a fixed, worldwide answer, and a never-seen-before name
  whose answer we know in advance, must both come back with exactly the right address. This
  drops resolvers that redirect or poison answers, and resolvers that answer NXDOMAIN for names
  that exist, which would silently hide real subdomains.
- **Real NXDOMAIN.** Made-up names under popular domains, several of them often censored,
  must come back as NXDOMAIN with no records. This drops resolvers that hijack NXDOMAIN
  (search or ad pages), that answer blocked domains with a block page, and that only answer
  from their cache, which refuse every new name and waste brute-force queries.
- **No filtering services.** Known filtering resolvers (family, malware and ad-blocking
  services) are left out, since they can hide hosts on purpose.

A resolver that times out is retried once, then dropped. The list only has resolvers that
passed the latest run.

## Notes

- IPv4 only.
- Passing from our checkers does not guarantee a resolver answers your network: some
  rate-limit or block ranges. Keep your tool's retries and rate limits on.
- Use these resolvers only for testing that you are authorized to do.

## Disclaimer

These lists are provided "as is", without warranty of any kind, under the [MIT License](LICENSE).
Resolvers are third-party servers we do not run: they can change, go down or start filtering at
any time. You are responsible for how you use them. The authors are not liable for any misuse or
damage.
