FactorDB
http://www.factordb.com/
https://www.alpertron.com.ar/ECM.HTM (Insane factoring)
##### Factoring with sage
```python
sage: factor(1139341176436985311500688692031588906553994277340492337220185900569)
1054782225375278316879059187384479 * 1080167212745381614630126356784711
sage:
```
#### Cryptodome

##### XOR 2 strings
```python
from Crypto.Util.strxor import strxor

strxor(b"Hello", b"World")
```

##### Long to bytes and bytes to long
```python
from Crypto.Util.number import long_to_bytes, bytes_to_long

print(long_to_bytes(0x68656c6c6f))

print(bytes_to_long(b'hello'))
```

### FactorDB
```python
from factordb.factordb import FactorDB

f = FactorDB(n)
f.connect()
factors = f.get_factor_list()
```


#### RSA

1. Choose two large random primes p and q.  
2. Calculate n = p\*q.  
3. Select a smaller odd integer a that is relatively prime with φ(n), where  
	$φ(n) = (p −1) (q −1)$  
1. Calculate b that is a multiplicative inverse to a modulo φ(n),that is,  
	$b = a^{−1} (mod φ(n))$  
1. Select (a, n) as a public key.  
2. Keep (b, n) as a secret key.  
3. Encryption function is defined as following:  
	$e_{K}(x) = x^a (mod n)$  
1. Decryption function is defined as following:  
	$d_{K}(y) = y^b (mod n)$


Vladimir hefte:
![[Numbertheory-lecturenotes-examples-with-solutions.pdf]]


### Large Public Exponent
Wiener-attack
https://github.com/orisano/owiener


### Mersene twister
Random numbers generated get seed
https://ctftime.org/writeup/28651

https://github.com/qxxxb/ctf/blob/master/2021/zh3r0_ctf/real_mersenne/solve.py

https://cor.team/posts/zh3r0-ctf-v2-twist_and_shout/

https://cor.team/posts/zh3r0-ctf-v2-real-mersenne/

https://github.com/icemonster/symbolic_mersenne_cracker/blob/main/main.py


### Quadratic Residue
Når man ganger med 4 så forblir det en quadratic residue. 
Når man ganger med 2 så blir det en quadratic non-residue. 
