
- Selected the algorithm based on the problem characteristics and on the prototyping objectives.
- Coded an end-to-end workflow (Qiskit patterns):
  - Parsing of the LP file
  - Rescaling of constraints
  - Derivation of the problem objective function
  - Transpilation
  - Quantum circuit execution
  - Classical optimizer (NFT)
  - Definition of the quantum objective function (CVaR)
  - Local search for solution polishing
- Validated the workflow and the algorithm selection on a small instance, by running simulations.
- Designed experiments for simulator and hardware runs.


Material covered:
- `_experiments/sbo_steps1to3.py`: high-level commands (steps 1 to 3 of the Qiskit Patterns)
- `_experiments/doe.py` design of experiments
- `src/step_1.py` implementing step1 (PROBLEM MAPPING), specifically
  - `problem_mapping()`: translation of string descriptors of the design of experiments into Qiskit objects; among other things, `problem_mapping()` defines the `bilinear` entanglement structure and the `bfcd` ansatz, inspired to fig. S1 of https://arxiv.org/pdf/2405.13898
  - `model_to_obj()`: translation from docplex object into a objective function including constraints as penalties
- `src/sbo/src/patterns/building_blocks/step_3.py` implementing step3 (EXECUTION)
  - `__init__()` instantiating the `BestValueMonitor` and `OptimizationMonitor` (see below)
  - `_sampler_result_to_cvar()` defining the CVaR aggregation rule from $[f(x)]_{x \sim X_\theta}$ to $g(\theta)$
- `src/sbo/src/optimizer/optimization_monitor.py`
  - `BastValueMonitor.cost()` which is a wrapper for the $f(x)$ function
  - `OptimizationMonitor.cost()` which is a wrapper for the $g(\theta)$ function
- `src/sbo/src/optimizer/optimization_wrapper.py` where the minimize method of Scipy is called
- `src/sbo/src/optimizer/nft.py`: a refined, Scipy-compatible implementation of the NFT optimizer https://arxiv.org/pdf/1903.12166
- `_experiments/sbo_step4.py` where step4 (POSTPROCESSING - LOCAL SEARCH) is called
- `src/sbo/src/optimizer/local_search.py` the implementation of the local search proposed as Algorithm 1&2 in https://arxiv.org/pdf/2406.01743 (with some generalizations)

> [!NOTE]  
> You may have noticed that some files are included in the `src/sbo/src` folder, while other are directly in `src`. The former is (an extract of) another more industrialized repo, whereas in the latter contains developments for the project that either are problem-specific or not yet integrated in the overall repo.



- Simulator experiments
  - 31 qubits
  - noiseless simulator
  - NFT optimizer
  - various ansatze (TwoLocal, BFCD), entanglements (full, bilinear) and repetitions (1 to 3)
  - various cvar alphas (.1, .15, .2)
  - with and without local search post-processing

Material covered:
- `_experiments/analysis31_one_run.ipynb` Loading experiment outputs from disk files. Algorithm convergence plots. Effect of post-processing.
- `_experiments/analysis31_step3_TwoLocal.ipynb` Experiments with the TwoLocal ansatz without postprocessing: comparison of different settings.
  - Parameters: entanglement, alpha, reps.
  - Dimensions: number of runs hitting optimality, relative gap, last iteration where an improvement happens, hamming weight of the best solution found.
- `_experiments/analysis31_step3_bfcd.ipynb` Experiments with the bfcd ansatz without postprocessing.
- `_experiments/analysis31_step4_TwoLocal.ipynb` Experiments with the TwoLocal ansatz: comparison with and without postprocessing (local search).
  - We enabled postprocessing on all the shots of the last 20 iterations.
  - Parameters: entanglement, alpha, reps.
  - Dimensions: number of runs hitting optimality, relative gap.




- Qiskit code assistant
- Quantum serverless
- Transpilation for hardware

Material covered:
- Qiskit code assistant
  - [Documentation and installation guide](https://docs.quantum.ibm.com/guides/qiskit-code-assistant)
  - [Github repo](https://github.com/Qiskit/qiskit-code-assistant-vscode), potentially useful to compile the plugin form scratch
  - `misc/qiskit-assistant-demo.ipynb` demo
- Quantum serverless
  - [Documentation](https://docs.quantum.ibm.com/guides/serverless)
  - `misc/serverless/without_image/create_serverless.py` creation of serverless function
  - `src/serverless_runner.py` entry point for serverless execution
  - `_experiments/sbo_steps1to3.py` call to the serverless function
- Transpilation for hardware; introduction of "colored" entanglement
  - `_experiments/transpilation109bilin.ipynb` TwoLocal ansatz, 109 qubits, "bilinear" entanglement, multiple devices, multiple repetitions
  - `_experiments/transpilation109color.ipynb` TwoLocal ansatz, 109 qubits, "colored" entanglement, multiple devices, multiple repetitions
  - Qiskit addon - coloring: [documentation](https://qiskit.github.io/qiskit-addon-utils/how_tos/color_device_edges_to_improve_depth.html)

> [!NOTE]  
> The python packages currently installed in serverless are:

Package                                 | Version
----------------------------------------| -----------
aiobotocore                             | 2.15.2
aiohappyeyeballs                        | 2.4.4
aiohttp                                 | 3.11.10
aiohttp-cors                            | 0.7.0
aioitertools                            | 0.12.0
aiosignal                               | 1.3.2
annotated-types                         | 0.7.0
asttokens                               | 3.0.0
attrs                                   | 24.3.0
botocore                                | 1.35.36
cachetools                              | 5.5.0
certifi                                 | 2024.7.4
cffi                                    | 1.17.1
charset-normalizer                      | 3.4.0
click                                   | 8.1.7
cloudpickle                             | 2.2.1
colorful                                | 0.5.6
comm                                    | 0.2.2
cryptography                            | 44.0.0
decorator                               | 5.1.1
Deprecated                              | 1.2.15
dill                                    | 0.3.9
distlib                                 | 0.3.9
executing                               | 2.1.0
filelock                                | 3.16.1
frozenlist                              | 1.5.0
fsspec                                  | 2024.10.0
google-api-core                         | 2.24.0
google-auth                             | 2.37.0
googleapis-common-protos                | 1.66.0
grpcio                                  | 1.68.1
ibm-cloud-sdk-core                      | 3.22.0
ibm-platform-services                   | 0.59.0
idna                                    | 3.10
importlib_metadata                      | 8.4.0
ipython                                 | 8.30.0
ipywidgets                              | 8.1.5
jedi                                    | 0.19.2
Jinja2                                  | 3.1.4
jmespath                                | 1.0.1
jsonschema                              | 4.23.0
jsonschema-specifications               | 2024.10.1
jupyterlab_widgets                      | 3.0.13
linkify-it-py                           | 2.0.3
markdown-it-py                          | 3.0.0
MarkupSafe                              | 3.0.2
matplotlib-inline                       | 0.1.7
mdit-py-plugins                         | 0.4.2
mdurl                                   | 0.1.2
memray                                  | 1.15.0
mpmath                                  | 1.3.0
msgpack                                 | 1.1.0
multidict                               | 6.1.0
numpy                                   | 2.2.0
opencensus                              | 0.11.4
opencensus-context                      | 0.1.3
opentelemetry-api                       | 1.29.0
opentelemetry-exporter-otlp-proto-common| 1.29.0
opentelemetry-exporter-otlp-proto-grpc  | 1.29.0
opentelemetry-instrumentation           | 0.50b0
opentelemetry-instrumentation-requests  | 0.50b0
opentelemetry-proto                     | 1.29.0
opentelemetry-sdk                       | 1.29.0
opentelemetry-semantic-conventions      | 0.50b0
opentelemetry-util-http                 | 0.50b0
packaging                               | 24.2
pandas                                  | 2.2.3
parso                                   | 0.8.4
pbr                                     | 6.1.0
pexpect                                 | 4.9.0
pip                                     | 24.3.1
platformdirs                            | 4.3.6
prometheus_client                       | 0.21.1
prompt_toolkit                          | 3.0.48
propcache                               | 0.2.1
proto-plus                              | 1.25.0
protobuf                                | 5.29.1
ptyprocess                              | 0.7.0
pure_eval                               | 0.2.3
py-spy                                  | 0.4.0
pyarrow                                 | 18.1.0
pyasn1                                  | 0.6.1
pyasn1_modules                          | 0.4.1
pycparser                               | 2.22
pydantic                                | 2.9.2
pydantic_core                           | 2.23.4
Pygments                                | 2.18.0
PyJWT                                   | 2.10.1
pyspnego                                | 0.11.2
python-dateutil                         | 2.9.0.post0
pytz                                    | 2024.2
PyYAML                                  | 6.0.2
qiskit                                  | 1.3.1
qiskit-ibm-runtime                      | 0.34.0
qiskit_serverless                       | 0.18.1
ray                                     | 2.34.0
referencing                             | 0.35.1
requests                                | 2.32.3
requests_ntlm                           | 1.3.0
rich                                    | 13.9.4
rpds-py                                 | 0.22.3
rsa                                     | 4.9
rustworkx                               | 0.15.1
s3fs                                    | 2024.10.0
scipy                                   | 1.14.1
setuptools                              | 75.6.0
six                                     | 1.17.0
smart-open                              | 7.0.5
stack-data                              | 0.6.3
stevedore                               | 5.4.0
symengine                               | 0.13.0
sympy                                   | 1.13.3
textual                                 | 1.0.0
tqdm                                    | 4.67.1
traitlets                               | 5.14.3
typing_extensions                       | 4.12.2
tzdata                                  | 2024.2
uc-micro-py                             | 1.0.3
urllib3                                 | 2.2.3
virtualenv                              | 20.28.0
wcwidth                                 | 0.2.13
websocket-client                        | 1.8.0
widgetsnbextension                      | 4.0.13
wrapt                                   | 1.17.0
yarl                                    | 1.18.3
zipp                                    | 3.19.1



- Comparison between simulator and hardware, on 31 and 109 bonds.

Material covered:
- 31-qubit hardware results: `_experiments/analysis31hw_one_run.ipynb` compared with simulator: `_experiments/analysis31_one_run.ipynb` and `_experiments/analysis31_step4_TwoLocal.ipynb`
- 109-qubit hardware results: `_experiments/analysis109hw.ipynb` compared with simulator: `_experiments/analysis109.ipynb`
- qubits that have converged to a stable configuration (for the 109-qubit case): `_experiments/qubit_convergence109.ipynb`
