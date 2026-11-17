# BioVerify — Multimodal Biometric Verification System

**Video-based identity verification, speech-command recognition, and permission-aware access control with an experimental fingerprint extension.**

BioVerify is a student-built biometric verification project that processes short video recordings to verify a claimed identity using facial and voice characteristics. It also recognizes spoken commands and checks the verified user's permissions before allowing a **simulated** action. The project includes a separate fingerprint verification experiment and a three-modality fusion comparison.

> **Project status:** Notebook-based research prototype. The core models, evaluation, and simulated command authorization are implemented. Live recording is experimental; this is not a deployment-ready biometric security system.

## Key features

- **Video preprocessing:** extracts representative facial frames and mono, 16 kHz audio tracks from user recordings.
- **Face verification:** detects/crops faces with OpenCV and extracts **HOG** and **LBP** features; compares **SVM** and **Random Forest** models.
- **Voice verification:** extracts **MFCC**, **zero-crossing rate (ZCR)**, and **RMS energy** statistics; compares **SVM** and **KNN** models.
- **Early feature fusion:** combines face and voice feature vectors for multimodal verification.
- **Hierarchical verification:** evaluates Face → Voice → Face+Voice Fusion against a claimed user identity.
- **Voice commands and permissions:** classifies the isolated commands **Open**, **Close**, and **Status**, then checks per-user permissions. Authorized commands update a *simulated* open/closed state; no physical device is controlled.
- **Fingerprint bonus:** trains and evaluates a fingerprint verification model using an online fingerprint dataset mapped to team identities **for demonstration only**. A separate **Face+Voice+Fingerprint** early-fusion model is included for comparison; its decision does not gate operational commands.
- **Evaluation:** classification metrics, verification metrics (**FAR**, **FRR**, accuracy), confusion matrices, and command-authorization tests.

## System workflow

```text
                          Video + Claimed User ID
                                    |
                         Extract facial frame + audio
                                    |
                 Face Verification -> Voice Verification
                                    |
                        Face + Voice Early Fusion
                                    |
                            Identity verified?
                             /             \
                           No               Yes
                           |                 |
                        Reject        Recognize spoken command
                                             |
                                       Check permissions
                                        /         \
                                      Deny        Simulated action

              Separate experiment: Fingerprint Verification
                          + Face/Voice/Fingerprint Fusion
                   (evaluated only; not an authorization gate)
```

## Tools and technologies

| Area | Tools / methods |
| --- | --- |
| Development | Python, Jupyter Notebook, Google Colab |
| Video and face processing | OpenCV, Haar Cascade, HOG, LBP, scikit-image |
| Audio processing | FFmpeg, librosa, SoundFile, MFCC, ZCR, RMS |
| Machine learning | scikit-learn: SVM, Random Forest, KNN, StandardScaler |
| Fingerprint image processing | Pillow, OpenCV, HOG / LBP |
| Evaluation and visualization | NumPy, pandas, Matplotlib, scikit-learn metrics |
| Model persistence | joblib |

## Data and privacy

The team **collected its own face videos and voice recordings**. Each recording is labeled by user identity, spoken command, and dataset split (train, validation, or test).

**Team recordings are private and are intentionally NOT included in this repository.** This also applies to extracted facial frames, audio clips, and other personally identifiable biometric data. Please do not open an issue requesting these files.

The fingerprint experiment uses the **FVC2002 DB1_A** fingerprint images, with identities assigned to team users *artificially for the proof of concept*. This mapping is not a claim that those fingerprints belong to team members. Fingerprint data may be added under `data/fingerprints/` **only when the dataset terms allow redistribution**. If the images are not included, download an authorized copy separately and supply its ZIP to the notebook.

Fingerprint dataset information: [FVC fingerprint datasets — IAPR TC4](https://iapr-tc4.org/fingerprint-datasets/).

## Repository structure

```text
BioVerify-Multimodal/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── Task_2_Supervised.ipynb
└── data/
    └── fingerprints/
        └── README.md
```

The notebook generates trained models, feature CSVs, evaluation reports, confusion matrices, permissions, and an exported Python pipeline **at runtime**. These generated artifacts are excluded from the public repository by default because some include references to private input data.

## Running the project in Google Colab

1. Open [`notebooks/Task_2_Supervised.ipynb`](notebooks/Task_2_Supervised.ipynb) in **Google Colab**.
2. Run the setup cells to install dependencies. A Colab runtime with **FFmpeg** is required.
3. When prompted, upload two ZIP files:
   - Your **own authorized team recordings** (kept private, not provided by this repository).
   - The **FVC2002 DB1_A** fingerprint dataset ZIP (obtained in accordance with its usage terms).
4. Run cells in order to preprocess recordings, train models, select thresholds on validation data, and view the test reports.
5. Review generated files under `/content/team_biometrics/outputs/`. The notebook provides an optional ZIP export.
6. For new-input testing, provide a claimed identity and a fresh video, or use the optional microphone demo. The interactive recording workflows depend on browser permissions and may require runtime-specific adjustments.

### Expected private recording layout

```text
team_dataset/
  team_user01/
    train/      open01.mp4, close01.mp4, status01.mp4, ...
    val/        open12.mp4, close12.mp4, status12.mp4, ...
    test/       open15.mp4, close15.mp4, status15.mp4, ...
  team_user02/
  team_user03/
  team_user04/
```

Recordings should be independent takes, not renamed copies of the same video. The notebook checks for exact duplicate files and maintains split information. **No private recordings are included to reproduce the reported model training publicly.**

## Evaluation and limitations

The notebook compares face-only, voice-only, fingerprint-only, two-modality fusion, three-modality fusion, and hierarchical verification. It reports classification and verification metrics, including false acceptance and false rejection rates, as well as command recognition and permission-decision results.

Results are from a **small student-collected dataset** and a **simulated fingerprint mapping**. Some recordings may be similar or duplicated, and the fingerprint experiment reuses impressions across paired video samples. These results must not be interpreted as verified real-world security performance. The system does not claim robust spoofing/liveness protection or reliable recognition of arbitrary, unseen words.

## Team

Developed as a four-member student project. Components cover face processing, voice and command modeling, fingerprint/data organization, and multimodal fusion/integration.

## Disclaimer

For educational demonstration and research comparison only. Do not use this prototype to make consequential access-control decisions.
