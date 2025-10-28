## MusicBrainz (via AcoustID) API Integration Workflow

This document outlines the operational necessity, technical implementation, and required compliance steps for So Litty Records (SLR) developers utilizing the AcoustID API to access and contribute to the MusicBrainz metadata database. This integration is crucial for enhancing music discoverability outside the Cardano ecosystem and supporting our mandatory metadata standards (CIP-60).

### I. Purpose and Rationale for SLR

The integration with MusicBrainz, accessed via the AcoustID Web Service API, serves to enhance the accuracy and interoperability of SLR's music assets.

1.  **Metadata Enrichment:** Used for **linking with databases like MusicBrainz** to ensure music is discoverable outside the Cardano ecosystem.
2.  **Rights Validation:** It supports the ongoing exploration of **Audio Analysis Tools** and services like Chromaprint (used by AcoustID) for **audio fingerprinting** to validate copyright and authenticity.
3.  **Unique Identifier (UID) Sourcing:** The look-up process retrieves the MusicBrainz Recording ID, which helps match sound recordings to necessary identifiers like the **ISRC** (International Standard Recording Code) and the **ISWC** (International Standard Musical Work Code), both of which are mandated fields within the **CIP-60** metadata standard.
4.  **Data Quality:** Submission of fingerprints ensures that compositions registered by SLR's Publishing Team (**The Psyence Lab LLC**) are accurately cataloged across global databases, supporting the robust registration process with PROs (ASCAP, BMI).

### II. SLR Usage Guidelines and Compliance

As developers are expected to adhere to the SLR **NDA** and **Code of Conduct**, strict compliance with the AcoustID Web Service guidelines is mandatory.

| Guideline | Requirement | Source(s) |
| :--- | :--- | :--- |
| **Commercial Use** | **Prohibited.** This service is provided free for **non-commercial use only**. Commercial applications require separate sign-up and explicit approval from the service provider. | |
| **Rate Limiting** | Developers must implement rate limiting: **Do not make more than 3 requests per second**. | |
| **Code Storage** | All source code used for API submissions, particularly for projects like **StripperCoin Tease Tokens** or JukeBoys, must be protected and stored in the **SLR GitHub repository**. | |
| **Legal Adherence** | Do not use the service in connection with any illegal products or services. | |

### III. SLR Workflow for Metadata Retrieval and Submission

The following steps define the standard process for SLR team members (Publishing/Development) seeking MusicBrainz data via the AcoustID API:

#### **A. Data Preparation**

1.  **Secure Final Audio:** Ensure the finalized master recording is available (preferably in lossless **WAV format**).
2.  **Generate Chromaprint Fingerprint:** Use the Chromaprint tool (external to this repository, mentioned in source as the fingerprint generator) to create the unique **audio fingerprint data**.
3.  **Gather Unique Identifiers:** Ensure all mandated SLR metadata is ready, including song title, duration, and **ISRC**. The **ISWC** (International Standard Musical Work Code) and **IPI number** (Interested Parties Information) are necessary for linking the composition to the correct writer and publisher.

#### **B. Lookup and Verification**

1.  **Execute Lookup Request:** Use the `Lookup by fingerprint` method, including the required `client` API key, `duration` (in seconds), and the `fingerprint` data.
    *   *Technical Step:* Specify desired metadata in the `meta` parameter (e.g., `meta=recordings,recordingids,releases`) to retrieve MusicBrainz recording IDs and comprehensive details.
2.  **Retrieve MusicBrainz IDs (MBID):** The response will return a MusicBrainz Recording ID (`mbid`) if a match is found, along with a match confidence `score`.
3.  **Verify Data Integrity:** Compare the returned MBID and associated metadata (e.g., track title, artist name) against the information previously collected via the **SLR Artist Intake Form** and the **Submission Form for Required Information and Media**.

#### **C. Data Submission and Enrichment**

1.  **Submission Necessity:** If the track is a new release or lacks accurate metadata in the public database, submit the new fingerprint and its corresponding metadata.
2.  **Execute Submission Request:** Use the `Submit` method, requiring the `client` API key, a unique `user` API key, the `duration`, and the audio `fingerprint`.
    *   **Crucial Data Fields:** During submission, prioritize supplying textual metadata and the corresponding **MusicBrainz recording ID (`mbid`)**, if available from embedded tags. Other valuable fields include `track`, `artist`, `album`, and `year`.
    *   *Note:* While the AcoustID API supports textual metadata, SLR's internal workflow should prioritize verification and inclusion of the **ISRC** and **ISWC** whenever possible, as these are the global unique identifiers essential for traditional royalty collection and are required for CIP-60 compliance.
3.  **Track Status:** Submissions are processed **asynchronously**. Developers must check the submission status endpoint if waiting for successful import.

### IV. Key AcoustID API Methods (Reference)

| Method | URL | Required Parameters | Purpose for SLR | Source(s) |
| :--- | :--- | :--- | :--- | :--- |
| **Lookup by fingerprint** | `https://api.acoustid.org/v2/lookup` | `client`, `duration`, `fingerprint` | Retrieve MusicBrainz metadata and Recording IDs based on audio fingerprint. | |
| **Submit** | `https://api.acoustid.org/v2/submit` | `client`, `user`, `duration.#`, `fingerprint.#` | Submit new audio fingerprints and metadata (e.g., `mbid`, `isrc`, `iswc`) to the database. | |
| **Get submission status** | `https://api.acoustid.org/v2/submission_status` | `client`, `id` | Check if an asynchronous submission has been successfully imported. | |
| **List AcoustIDs by MBID** | `https://api.acoustid.org/v2/track/list_by_mbid` | `mbid` | Retrieve AcoustID track IDs associated with a specific MusicBrainz ID. | |

### V. Connection to CIP-60 Standardization

The metadata sourced and validated through the AcoustID/MusicBrainz interface directly informs the required fields in SLR’s CIP-60 v3 compliant tokens.

**Mandatory CIP-60 Fields relying on Verified Metadata:**

*   **ISRC** (International Standard Recording Code): Unique ID for the sound recording.
*   **ISWC** (International Standard Musical Work Code): Unique ID for the underlying musical composition.
*   **IPI/ISNI** (Interested Parties/Name Identifier): Unique IDs for creators, artists, and publishers (used in PRO registration and CIP-60's author/artist arrays).
*   **Precise Ownership Attribution:** Requires accurate listing of authors, roles, and **precise percentage ownership shares** for automated royalty distribution.
