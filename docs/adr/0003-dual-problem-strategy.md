# Use two PDE problems: Burgers inverse (primary) + Allen-Cahn forward (bonus)

We implement two PDE problems with different roles. Burgers equation inverse problem (recovering viscosity ν) is the primary experiment—it's a well-documented standard benchmark where PINN training difficulties (gradient pathology, loss imbalance) are known and reproducible, making it reliable for demonstrating the Agent's value. Allen-Cahn equation forward problem is a bonus demonstration—it's notoriously harder for PINN (stiff reaction term, sharp phase transitions), and vanilla configurations consistently fail, making the Agent's impact more dramatic: from "cannot train" to "successfully trained."

Status: accepted

Considered Options:
- Burgers only: safe but underwhelming; Burgers is the "MNIST of PINNs" and may not impress reviewers
- Allen-Cahn only: more impressive forward problem but inverse problem is unsolved in current literature, too risky for a course project
- Burgers inverse + Allen-Cahn forward: progressive difficulty, Burgers validates the system, Allen-Cahn demonstrates its real value
