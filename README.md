
# Aetherion NGES (Next-Gen Encryption Scheme)

## Overview

**Aetherion** is a post-quantum cryptographic (PQC) encryption scheme designed to withstand attacks from quantum computers. It leverages lattice-based cryptography, specifically utilizing ring polynomial structures to provide a high level of security while maintaining efficiency in terms of both speed and computational resources. Aetherion ensures robustness against both classical and quantum adversaries.

Aetherion’s parameter set for **NGES-128** is based on the structure of, and the design of Aetherion is also inspired by, **Kyber (KEM)** and **Dilithium (DSA)**, drawing from concepts outlined in **FIPS 203** and **FIPS 204**, which form the basis for its advanced optimizations.

## Features

- **Lattice-based Security**: Aetherion uses lattice problems such as Learning With Errors (LWE) to ensure its resistance against quantum attacks.
- **Efficient Key Generation**: Optimized key generation process suitable for large-scale applications.
- **Fast Encryption and Decryption**: Advanced compression techniques and the Number-Theoretic Transform (NTT) make encryption and decryption highly efficient.
- **Lossy Compression**: Utilizes polynomial compression techniques that allow for optimized storage and transmission without compromising security.
- **Support for Parallelization**: Supports parallel encryption and decryption of large datasets, providing scalability in high-performance computing environments.
  
## Parameters

Aetherion operates on a customizable set of parameters that determine its security level and performance. The main parameters include:

- **n**: Degree of polynomials used.
- **q**: Prime modulus used in polynomial operations.
- **k**: Number of rows/columns in the key matrix.
- **eta**: Range for secret and error coefficients, controlling noise generation.
- **du, dv**: Compression degrees used during encryption and decryption processes.

The following table outlines the default parameters for different security levels:

|             | k  | q     | n    | eta | du | dv |
|-------------|----|-------|------|-----|----|----|
| ML_NGES_128 | 2  | 3329  | 256  | 2   | 10 | 4  |
| ML_NGES_192 | 3  | 7681  | 512  | 3   | 11 | 5  |
| ML_NGES_256 | 4  | 12289 | 1024 | 4   | 12 | 6  |
| ML_NGES_512 | 5  | 12289 | 2048 | 5   | 17 | 12 |

These parameters are fine-tuned to provide various levels of security, from ML_NGES_128 for standard security to ML_NGES_512 for highly secure applications.

## Performance Statistics

The following table summarizes the performance of Aetherion for different parameter sets. These statistics are based on testing for `1000` iterations and include key generation, encryption, decryption, and total processing time of random byte data using `os.urandom` with a size of `26000 bytes`.

|                 | Key Generation (s)                                                | Encryption (s)        | Decryption (s)        | Total Time (s)        |
|-----------------|-------------------------------------------------------------------|-----------------------|-----------------------|-----------------------|
| **ML_NGES_128** | Min: 0.000999<br>Max: 0.002002<br>Mean: 0.001600<br>Std Dev: 0.000516 | Min: 0.658629<br>Max: 0.686016<br>Mean: 0.674162<br>Std Dev: 0.009434 | Min: 0.603091<br>Max: 0.637735<br>Mean: 0.614629<br>Std Dev: 0.010110 | Min: 1.263719<br>Max: 1.325752<br>Mean: 1.290390<br>Std Dev: 0.016823 |
| **ML_NGES_192** | Min: 0.004999<br>Max: 0.005998<br>Mean: 0.005150<br>Std Dev: 0.000337   | Min: 0.678202<br>Max: 0.707689<br>Mean: 0.689915<br>Std Dev: 0.007893 | Min: 0.585892<br>Max: 0.609389<br>Mean: 0.597545<br>Std Dev: 0.007539 | Min: 1.274833<br>Max: 1.308028<br>Mean: 1.292610<br>Std Dev: 0.011179 |
| **ML_NGES_256** | Min: 0.017000<br>Max: 0.019006<br>Mean: 0.018401<br>Std Dev: 0.000699   | Min: 0.774127<br>Max: 0.832551<br>Mean: 0.794047<br>Std Dev: 0.019520 | Min: 0.617378<br>Max: 0.664145<br>Mean: 0.631041<br>Std Dev: 0.015376 | Min: 1.418145<br>Max: 1.480398<br>Mean: 1.443489<br>Std Dev: 0.021158 |
| **ML_NGES_512** | Min: 0.054000<br>Max: 0.057053<br>Mean: 0.055310<br>Std Dev: 0.000987   | Min: 0.911778<br>Max: 0.928156<br>Mean: 0.924351<br>Std Dev: 0.005661 | Min: 0.665212<br>Max: 0.678384<br>Mean: 0.670942<br>Std Dev: 0.004579 | Min: 1.635434<br>Max: 1.661194<br>Mean: 1.650602<br>Std Dev: 0.008543 |

These statistics show that the encryption and decryption times increase with larger parameter sets, as expected due to the higher degree of polynomials and matrix dimensions. However, Aetherion maintains efficient processing times across all security levels.

Please Note, that the times are lower when en/decrypting a `string` message with a size of `26000 bytes`:

|                | **ML_NGES_128** | **ML_NGES_192** | **ML_NGES_256** | **ML_NGES_512** |
|----------------|-----------------|-----------------|-----------------|-----------------|
| Total Time (s) | 0.375511        | 0.384196        | 0.400546        | 0.498879        |

## Algorithm Overview

### Key Generation

1. **Matrix Generation**: A matrix `A` is generated from a seed using an extendable-output function (XOF).
2. **Secret and Error Vector Generation**: The secret vector `s` and error vector `e` are sampled from a centered binomial distribution.
3. **Cipher Key Computation**: The cipher key is computed as `t_hat = A_hat * s_hat + e_hat` where `A_hat`, `s_hat`, and `e_hat` are in NTT form.

### Encryption

1. **Message Compression**: The message is compressed using polynomial techniques.
2. **Random Vector Generation**: A random vector `y` and error vectors `e1`, `e2` are generated.
3. **Ciphertext Computation**: Two ciphertext components `u` and `v` are computed using matrix-vector multiplication and polynomial operations.
4. **Authentication Tag**: HMAC is applied to ensure message integrity.

### Decryption

1. **Key Parsing**: The cipher key is used to parse and decode the ciphertext.
2. **NTT Transform**: The cipher key is transformed into NTT form, and decryption is performed using inverse NTT operations.
3. **Message Reconstruction**: The original message is decompressed and authenticated.

## Security Considerations

Aetherion is designed to be resistant to both classical and quantum attacks. The use of lattice-based cryptography and polynomial operations provides strong protection against attempts to break the scheme using quantum computers.

## Implementation

The reference implementation is optimized for both speed and security. Key operations, such as polynomial multiplication and NTT, are highly efficient and benefit from modular arithmetic over large primes.

The implementation also supports parallel execution, making it suitable for high-performance computing environments.

## Usage

### Key Generation
```python
from quantum.lib import ML_NGES_256

# Generate the cipher key
key = ML_NGES_256.keygen()
# Encrypt a message
ciphertext, tag = ML_NGES_256.encrypt(key, message)
# Decrypt a message
message = ML_NGES_256.decrypt(key, ciphertext, tag)
```

## Acknowledgements

Aetherion draws significant inspiration from the post-quantum algorithms **Kyber** (a Key Encapsulation Mechanism) and **Dilithium** (a Digital Signature Algorithm), both of which are part of the NIST Post-Quantum Cryptography Standardization Process and are detailed in [FIPS 203](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.203.pdf) and [FIPS 204](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.204.pdf). 

The development of Aetherion builds upon the logic and mathematical foundations established by these schemes:

- **Parameter Selection**: The parameters for **NGES-128** are based on those used in Kyber, providing a foundation for optimizing Aetherion's performance and security.
- **Algorithmic Structure**: Aetherion adopts similar structures in key generation, encryption, and decryption processes, adapting them to create an efficient encryption scheme.
- **Security Foundations**: Leveraging the security proofs and cryptographic assumptions used in Kyber and Dilithium, Aetherion ensures robustness against both classical and quantum attacks.

By building upon the established work of Kyber and Dilithium, Aetherion benefits from their proven security models while introducing optimizations specific to encryption schemes.

## References

- [NIST FIPS 203](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.203.pdf)
- [NIST FIPS 204](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.204.pdf)
