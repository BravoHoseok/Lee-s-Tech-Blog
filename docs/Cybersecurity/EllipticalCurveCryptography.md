---
layout: default
title: 0_Elliptical Curve Cryptography (ECC)
parent: Topic_Cybersecurity
nav_order: 1
---

# Elliptical Curve Cryptography (ECC)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition

Elliptical Curve Cryptography is a type of the trapdoor function. In theoretical computer science and cryptography, the trapdoor function is a function that is easy to compute in one direction, yet difficult to compute in the opposite direction (finding its inverse) without special information, called the "Trapdoor". In Publick Key Infrastructure (PKI) using ECC, the "Trapdoor" should be the 'public key'. Thus, the main purpose of using ECC is to create the public key by feeding a private key which is generated randomly (e.g., 32 bytes positive integer) into ECC function.

```scss
system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif, "Segoe UI Emoji"
```

ABCDEFGHIJKLMNOPQRSTUVWXYZ
abcdefghijklmnopqrstuvwxyz
{: .fs-5 .ls-10 .code-example }

For monospace type, like code snippets or the `<pre>` element, Just the Docs uses a native system font stack for monospace fonts:

```scss
"SFMono-Regular", Menlo, Consolas, Monospace
```

ABCDEFGHIJKLMNOPQRSTUVWXYZ
abcdefghijklmnopqrstuvwxyz
{: .fs-5 .ls-10 .text-mono .code-example }

---