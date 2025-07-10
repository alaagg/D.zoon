import mpmath as mp

# Set high precision
mp.dps = 50

# Constants
f = mp.mpf("1.0")
c = mp.mpf("0.844327")
b = mp.mpf("0.851884")
a = mp.mpf("0.190863")
d = mp.mpf("0.190859")
A = mp.mpf("1e6")
delta = mp.mpf("0.13")      # Optimized phase shift
gamma = mp.mpf("1.0")       # Spectral weight

# Log–log correction coefficients
betas = [
    mp.mpf("0.857133"),
    mp.mpf("-7.567221"),
    mp.mpf("-2.050830"),
    mp.mpf("0.413348"),
    mp.mpf("-0.074889")
]

def R_loglog(k):
    """Log–log polynomial correction."""
    x = mp.log(k) / mp.log(mp.log(k))
    return sum(beta * x**n for n, beta in enumerate(betas))

def theta_k_estimate(k):
    """Estimate the spectral angle theta_k for root k."""
    t_approx = (2 * mp.pi * k - a * mp.log(A) - d * mp.log(A + 1) + R_loglog(k)) / f
    return mp.atan2(t_approx, mp.mpf("0.5"))

def Gk(t, k):
    """Albasatneh Super-Equation G_k(t)."""
    phi_k = delta * k
    theta = theta_k_estimate(k)
    return (f * t
            + c * mp.sin(b * t + phi_k)
            - 2 * mp.pi * k
            + a * mp.log(A)
            + d * mp.log(A + 1)
            - mp.asin(1 / A)
            - gamma * mp.asin(theta / A)
            - R_loglog(k))

def compute_root(k):
    """Solve G_k(t) = 0 numerically."""
    t0 = (2 * mp.pi * k - a * mp.log(A) - d * mp.log(A + 1) + R_loglog(k)) / f
    return mp.findroot(lambda t: Gk(t, k), t0)

# Example: compute the 1st non-trivial zero
k = 1
t_k = compute_root(k)
print(f"Approximate zero t_{k} ≈ {t_k}")
