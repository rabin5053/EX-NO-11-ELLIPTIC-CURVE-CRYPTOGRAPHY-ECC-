# EX-NO-11-ELLIPTIC-CURVE-CRYPTOGRAPHY-ECC

## Aim:
To Implement ELLIPTIC CURVE CRYPTOGRAPHY(ECC)


## ALGORITHM:

1. Elliptic Curve Cryptography (ECC) is a public-key cryptography technique based on the algebraic structure of elliptic curves over finite fields.

2. Initialization:
   - Select an elliptic curve equation \( y^2 = x^3 + ax + b \) with parameters \( a \) and \( b \), along with a large prime \( p \) (defining the finite field).
   - Choose a base point \( G \) on the curve, which will be used for generating public keys.

3. Key Generation:
   - Each party selects a private key \( d \) (a random integer).
   - Calculate the public key as \( Q = d \times G \) (using elliptic curve point multiplication).

4. Encryption and Decryption:
   - Encryption: The sender uses the recipient’s public key and the base point \( G \) to encode the message.
   - Decryption: The recipient uses their private key to decode the message and retrieve the original plaintext.

5. Security: ECC’s security relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), making it highly secure with shorter key lengths compared to traditional methods like RSA.

## Program:

~~~
#include <stdio.h>
#include <stdlib.h>
#include <gmp.h>
typedef struct {
 mpz_t x, y;
} ECPoint;
typedef struct {
 mpz_t a, b, p;
} EllipticCurve;
void init_point(ECPoint *P) {
 mpz_init(P->x);
 mpz_init(P->y);
}
void init_curve(EllipticCurve *curve) {
 mpz_init(curve->a);
 mpz_init(curve->b);
 mpz_init(curve->p);
}
void point_addition(ECPoint *R, ECPoint *P, ECPoint *Q, EllipticCurve *curve) {
 mpz_t lambda, temp1, temp2;
 mpz_inits(lambda, temp1, temp2, NULL);
 
 mpz_sub(temp1, Q->y, P->y);
 mpz_sub(temp2, Q->x, P->x);
 mpz_invert(temp2, temp2, curve->p);
 mpz_mul(lambda, temp1, temp2);
 mpz_mod(lambda, lambda, curve->p);
 mpz_mul(temp1, lambda, lambda);
 mpz_sub(temp1, temp1, P->x);
 mpz_sub(temp1, temp1, Q->x);
 mpz_mod(R->x, temp1, curve->p);
 mpz_sub(temp1, P->x, R->x);
 mpz_mul(temp1, lambda, temp1);
 mpz_sub(temp1, temp1, P->y);
 mpz_mod(R->y, temp1, curve->p);
 mpz_clears(lambda, temp1, temp2, NULL);
}
void encrypt(ECPoint *C1, ECPoint *C2, ECPoint *Pm, ECPoint *G, mpz_t k, EllipticCurve 
*curve, mpz_t privKey) {
 init_point(C1);
 mpz_mul(C1->x, G->x, k);
 mpz_mod(C1->x, C1->x, curve->p);
 mpz_mul(C1->y, G->y, k);
 mpz_mod(C1->y, C1->y, curve->p);
 init_point(C2);
 ECPoint kPub;
 init_point(&kPub);
 
 mpz_mul(kPub.x, G->x, k);
 mpz_mod(kPub.x, kPub.x, curve->p);
 mpz_mul(kPub.y, G->y, k);
 mpz_mod(kPub.y, kPub.y, curve->p);
 point_addition(C2, Pm, &kPub, curve);
}
void decrypt(ECPoint *Pm_decrypted, ECPoint *C1, ECPoint *C2, mpz_t privKey, 
EllipticCurve *curve) {
 init_point(Pm_decrypted);
 ECPoint temp;
 init_point(&temp);
 
 mpz_mul(temp.x, C1->x, privKey);
 mpz_mod(temp.x, temp.x, curve->p);
 mpz_mul(temp.y, C1->y, privKey);
 mpz_mod(temp.y, temp.y, curve->p);
 point_addition(Pm_decrypted, C2, &temp, curve);
}
int main() {
 printf("\n***** ECC (Elliptic Curve Cryptography) *****\n\n");
 EllipticCurve curve;
 init_curve(&curve);
 
 mpz_set_str(curve.a, "2", 10);
 mpz_set_str(curve.b, "3", 10);
 mpz_set_str(curve.p, "17", 10);
 ECPoint G, Pm;
 init_point(&G);
 init_point(&Pm);
 mpz_set_str(G.x, "5", 10);
 mpz_set_str(G.y, "1", 10);
 mpz_set_str(Pm.x, "9", 10);
 mpz_set_str(Pm.y, "7", 10);
 mpz_t privKey, k;
 mpz_inits(privKey, k, NULL);
 mpz_set_str(privKey, "3", 10);
 mpz_set_str(k, "4", 10);
 ECPoint C1, C2;
 encrypt(&C1, &C2, &Pm, &G, k, &curve, privKey);
 printf("Encrypted Message: \n");
 gmp_printf("C1 = (%Zd, %Zd)\n", C1.x, C1.y);
 gmp_printf("C2 = (%Zd, %Zd)\n", C2.x, C2.y);
 ECPoint Pm_decrypted;
 decrypt(&Pm_decrypted, &C1, &C2, privKey, &curve);
 printf("Decrypted Message: \n");
 gmp_printf("Pm = (%Zd, %Zd)\n", Pm_decrypted.x, Pm_decrypted.y);
 mpz_clears(privKey, k, NULL);
 mpz_clears(curve.a, curve.b, curve.p, NULL);
 
 return 0;
}
~~~


## Output:

![image](https://github.com/user-attachments/assets/c35698a9-20c8-46ab-b5c9-b8963f958149)



## Result:
The program is executed successfully

