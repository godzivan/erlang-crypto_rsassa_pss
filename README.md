crypto_rsassa_pss
=================

[![Build Status](https://travis-ci.org/potatosalad/erlang-crypto_rsassa_pss.png?branch=master)](https://travis-ci.org/potatosalad/erlang-crypto_rsassa_pss)

RSASSA-PSS Public Key Cryptographic Signature Algorithm for Erlang.

Implemented closely after the [crypto-pubkey](https://github.com/vincenthz/hs-crypto-pubkey) project for Haskell.

See [RFC #3447](https://tools.ietf.org/html/rfc3447) and [RFC #4506](https://tools.ietf.org/html/rfc4056).

Build
-----

```bash
$ make
```

Usage
-----

First, let's generate a RSA key using `openssl`.

```bash
$ openssl genrsa -out rsa.pem 2048
Generating RSA private key, 2048 bit long modulus
...................+++
...............................................+++
e is 65537 (0x10001)
```

Now let's use our key to sign and verify a message.

### Erlang

```erl
1> {ok, Data} = file:read_file("rsa.pem").
{ok,<<"-----BEGIN RSA PRIVATE KEY-----\nMIIEowIBAAKCAQEAsBdtC3nJpzZ9tbcEIEBY4DPUdkiCLwjbHyZFzL+G72ambWsy\nkgnlSdW3JKR"...>>}
2> [PEMEntry | _] = public_key:pem_decode(Data).                
[{'RSAPrivateKey',<<48,130,4,163,2,1,0,2,130,1,1,0,176,23,
                    109,11,121,201,167,54,125,181,183,4,
                    32,64,...>>,
                  not_encrypted}]
3> #'RSAPrivateKey'{modulus=Modulus, publicExponent=PublicExponent} = PrivateKey = public_key:pem_entry_decode(PEMEntry).
#'RSAPrivateKey'{version = 'two-prime',
                 modulus = 22229493443645708705523581558090878957367586450909751107794238086962080015848054619397467933106360840581215574270745522166904017608027489665477935173052081890487447446310626920488171636893809207381146713861503360912041499077841558506105719930813981093031511085982222971766286284697738439495365308127623539168264485735318733143570874966874044788036410878275355326587265426041174941647279119175623719251367841389835668503095999900094770850870177317175179376777700441606775465664699045658323298203133653233096660719679323101930166930195443191172296135454691189674982978002129204335141598674600341635121716750790994857663,
                 publicExponent = 65537,
                 privateExponent = 9063156153229676924662253371869146981718142575465897883642248526536563742976639446881919269612615189287426646695978155123055302355715008680006262536032342464772946515181042026877091507664412194961994601437041210363149806298120549358120524841713071620699787543180874892131088843357547203920169690910021834483849134743555601203783807072109505533143770742728337175655038487885358069334754208953399452727458928811050452818025060994599655916130341740415252817366593987033651424125639494433027412278777046774479869728683039739604918658495070800181480018172418849752515334207484320393626124965634682208321416201446311633153,
                 prime1 = 164521523414867348932245957989117102915368563884993859002261566645808258344879910492420937163240199832331307100749119943409433286484548259511099355631330045213102436066823867425071151652762962497569166054934442815503467475819776236951618655403913958022234294442474216864427094473485506144195755649297015175787,
                 prime2 = 135116019972599473816048017685801033712236058170786861263736741860085553437437135622613712724374804076598166554483540794893509354227163240239139007233403699978088696404685225139789664570048411002777224365018069829297674225266705313820465805239182390476453868516395260463338929654977104656840607299316325284349,
                 exponent1 = 147569056142630304097428115330763687348780469954613104163891297033846417421386707636700983722231898117761929240130556277421611094690139690359651258394984594622991800078614740111927347586188229358333549863028003821758027253285773323663944810401203566052443974617489423747783424958868608468199693304366628623425,
                 exponent2 = 73698710132604687283553847265122443079654277300320703260674081988380277991486721807552564028842121569894176721899432882113283776882652613772807758847253949114496187350325506859820576764049629188340592937978560846360131842022199900104371441168882507779754893233678401144131966955711530283169525445605698764661,
                 coefficient = 54217906601613349900845012719441354443904142976126211011391339649360423968717271176279205528399064245082352536706243588002914706423805150006324479129342470987091660522921538081228784566290945826322084272584292895044988545674447894291015177179882935762102656930082543521724417149348501201209794855811489403546,
                 otherPrimeInfos = asn1_NOVALUE}
4> PublicKey = #'RSAPublicKey'{modulus=Modulus, publicExponent=PublicExponent}.
#'RSAPublicKey'{modulus = 22229493443645708705523581558090878957367586450909751107794238086962080015848054619397467933106360840581215574270745522166904017608027489665477935173052081890487447446310626920488171636893809207381146713861503360912041499077841558506105719930813981093031511085982222971766286284697738439495365308127623539168264485735318733143570874966874044788036410878275355326587265426041174941647279119175623719251367841389835668503095999900094770850870177317175179376777700441606775465664699045658323298203133653233096660719679323101930166930195443191172296135454691189674982978002129204335141598674600341635121716750790994857663,
                publicExponent = 65537}
5> Message = <<"hello world">>.
<<"hello world">>
6> Signature = crypto_rsassa_pss:sign(Message, sha256, PrivateKey).
<<38,90,46,243,100,214,118,52,91,101,152,27,12,35,237,53,
  226,149,135,146,212,87,237,40,107,225,154,69,195,...>>
7> true = crypto_rsassa_pss:verify(Message, sha256, Signature, PublicKey).
true
```
