---
title: Konta Ethereum
description: Wyjaśnienie kont Ethereum – ich struktury danych i powiązania z kryptografią kluczy.
lang: pl
sidebar: true
isOutdated: true
---

Konto Ethereum jest jednostką z saldem etheru (ETH), która może wysyłać transakcje na Ethereum. Konta mogą być kontrolowane przez użytkownika lub wdrażane jako inteligentne kontrakty.

## Warunki wstępne {#prerequisites}

Konta to bardzo przyjazny dla początkujących temat. Ale aby pomóc Ci lepiej zrozumieć tę stronę, zalecamy przeczytanie naszego [wprowadzenia do Ethereum](/en/developers/docs/intro-to-ethereum/).

## Rodzaje kont {#types-of-account}

Ethereum ma dwa typy kont:

- Zewnętrznie posiadane – kontrolowane przez każdego, kto ma klucze prywatne
- Kontrakt – inteligentny kontrakt realizowany w sieci, kontrolowany kodem. Dowiedz się więcej o [inteligentnych kontraktach](/en/developers/docs/smart-contracts/)

Oba typy kont mają możliwość:

- Odbierania, przechowywania i wysyłania ETH i tokenów
- Interakcji z wdrożonymi inteligentnymi kontraktami

### Kluczowe różnice {#key-differences}

**Zewnętrznie posiadane**

- Tworzenie konta nic nie kosztuje
- Może inicjować transakcje
- Transakcje pomiędzy kontami zewnętrznymi mogą być tylko przelewem ETH

**Kontrakt**

- Tworzenie konta wiąże się z kosztami, ponieważ korzystasz z pamięci sieciowej
- Może wysyłać transakcje tylko w odpowiedzi na otrzymanie transakcji
- Transakcje z konta zewnętrznie posiadanego na konto kontraktu mogą wyzwalać kod, który może wykonywać wiele różnych działań, takich jak przenoszenie tokenów lub nawet tworzenie nowego kontraktu

## Konto – analiza {#an-account-examined}

Konta Ethereum mają cztery pola:

- `nonce` – licznik, który wskazuje liczbę transakcji wysłanych z konta. Pole to gwarantuje, że transakcje są przetwarzane tylko raz. Dla konta kontraktu pole to oznacza liczbę kontraktów utworzonych przez konto.
- `balance` &ndash; ilość Wei należących do tego adresu. Wei to najmniejszy nominał ETH; 1e+18 Wei przypada na jeden ETH.
- `codeHash` &ndash; wszystkie tego rodzaju fragmenty kodu zawarte są w bazie danych stanu do późniejszego pobrania. Dla kont kontraktów pole to jest kodem, który jest haszowany i przechowywany jako codeHash. Dla kont posiadanych zewnętrznie pole codeHash jest haszem pustego ciągu znaków (ang. string).
<!--this hash refers to the code of this account on the Ethereum virtual machine (EVM). This EVM code gets executed if the account gets a message call. It cannot be changed unlike the other account fields.  -->
- `storageRoot` &ndash; jest znany także jako hasz pamięci. 256-bitowy skrót węzła głównego drzewa Merkle Patricia, który koduje zawartość pamięci konta (mapowanie między 256-bitowymi wartościami całkowitymi), zakodowany w trie jako mapowanie z 256-bitowego skrótu Keccak 256 -bitowe klucze liczb całkowitych do 256-bitowych wartości liczb całkowitych zakodowanych w RLP. To drzewo koduje hasz zawartości pamięci masowej tego konta i jest domyślnie puste.

![Schemat przedstawiający skład konta](../../../../../developers/docs/accounts/accounts.png) _Schemat zaadaptowany z [Ilustracja Ethereum EVM](https://takenobu-hs.github.io/downloads/ethereum_evm_illustrated.pdf)_

## Konta zewnętrzne i kluczowe pary {#externally-owned-accounts-and-key-pairs}

Konto składa się z pary kluczy kryptograficznych: publicznego i prywatnego. Pomogą one udowodnić, że transakcja została faktycznie podpisana przez nadawcę i zapobiec fałszerstwom. Twój klucz prywatny jest tym, czego używasz do podpisywania transakcji, więc zapewnia Ci opiekę nad środkami powiązanymi z Twoim kontem. Tak naprawdę nigdy nie trzymasz kryptowaluty, trzymasz klucze prywatne – środki są zawsze w księdze Ethereum.

Zapobiega to nadawaniu fałszywych transakcji przez złośliwe podmioty, ponieważ zawsze możesz zweryfikować nadawcę transakcji.

Jeśli Alice chce wysłać ether ze swojego konta na konto Boba, Alice musi utworzyć prośbę o transakcję i wysłać ją do sieci w celu weryfikacji. Wykorzystanie kryptografii klucza publicznego przez Ethereum zapewnia, że ​​Alice może udowodnić, że pierwotnie zainicjowała żądanie transakcji. Bez mechanizmów kryptograficznych złośliwa Eve mogłaby po prostu publicznie rozesłać zapytanie, które wygląda jak „Wyślij 5 ETH z konta Alice na konto Eve”, i nikt nie byłby w stanie sprawdzić, czy nie pochodziło od Alice.

## Tworzenie konta {#account-creation}

Kiedy chcesz utworzyć konto, większość bibliotek wygeneruje losowy klucz prywatny.

Klucz prywatny składa się z 64 znaków szesnastkowych i może być zaszyfrowany hasłem.

Przykład:

`fffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd036415f`

Klucz publiczny jest generowany z klucza prywatnego przy użyciu algorytmu cyfrowego z krzywą eliptyczną. Otrzymujesz publiczny adres na swoje konto, biorąc ostatnie 20 bajtów klucza publicznego i dodając `0x` na początek.

Oto przykład utworzenia konta w konsoli za pomocą `personal_newAccount`

```go
> personal.newAccount()
Passphrase:
Repeat passphrase:
"0x5e97870f263700f46aa00d967821199b9bc5a120"

> personal.newAccount("h4ck3r")
"0x3d80b31a78c30fc628f20b2c89d7ddbf6e53cedc"
```

[Dokumentacja GETH](https://geth.ethereum.org/docs)

Możliwe jest pozyskanie nowych kluczy publicznych z Twojego klucza prywatnego, ale nie możesz uzyskać klucza prywatnego z kluczy publicznych. Oznacza to, że ważne jest, aby klucz prywatny był bezpieczny i, jak sugeruje nazwa, **PRYWATNE**.

Potrzebujesz prywatnego klucza do podpisywania wiadomości i transakcji, które wygenerują podpis. Inni mogą wtedy wziąć podpis, aby uzyskać klucz publiczny, potwierdzając autora wiadomości. W swojej aplikacji możesz użyć biblioteki javascript do wysyłania transakcji do sieci.<!-- **WEB3JS example**

```jsx
web3.eth.accounts.recoverTransaction('0xf86180808401ef364594f0109fc8df283027b6285cc889f5aa624eac1f5580801ca031573280d608f75137e33fc14655f097867d691d5c4c44ebe5ae186070ac3d5ea0524410802cdc025034daefcdfa08e7d2ee3f0b9d9ae184b2001fe0aff07603d9');
> "0xF0109fC8DF283027b6285cc889F5aA624EaC1F55"
```

[Web3js documentation](https://web3js.readthedocs.io/)

[code for creating an account in JS?] + links to how to do it in other languages maybe?

`$ geth account new` -->## Konto kontraktowe {#contract-accounts}

Konta umowne mają również 42 znaki w układzie szesnastkowym:

Przykład:

`0x06012c8cf97bead5deae237070f9587f8e7a266d`

Adres kontraktu jest zazwyczaj podany w momencie realizacji zamówienia do Ethereum Blockchain. Adres pochodzi z adresu twórcy i liczby transakcji wysłanych z tego adresu („nonce”).<!-- @Sam Richards is there a line of code you can use to return your contract's address – in the same way that we have personal.newAccount() above? – Don't know if what I found below is helpful?

```jsx
ethers.utils.getContractAddress( transaction ) ⇒ string< Address >
```

TODO: add a contract address example--><!-- ## Managing an account

Most users will want to interact with their account via a wallet. Note that an account is not a wallet. A wallet is the keypair associated with a user-owned account, which allow a user to make transactions from or manage the account

For dapp development, you'll want access to dummy accounts with test ETH so you can experiment. When you create a local chain, you'll get test accounts wth fake ETH which you can then import using MetaMask and use on your dapp's frontend. -->

## Więcej o portfelach {#a-note-on-wallets}

Konto nie jest portfelem. Portfel jest parą kluczy powiązaną z kontem będącym własnością użytkownika, która pozwala użytkownikowi na dokonywanie transakcji z konta lub zarządzanie nim.

## Dodatkowo przeczytaj {#further-reading}

_Wiesz o zasobach społecznościowych, które Ci pomogły? Wyedytuj tę stronę i dodaj je!_

## Powiązane tematy {#related-topics}

- [Inteligentne kontrakty](/en/developers/docs/smart-contracts/)
- [Transakcje](/en/developers/docs/transactions/)
