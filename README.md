# Building an AI Voice Agent with Asterisk: Stream Extraction Methods for ASR Integration

To build an effective real-time AI Voice Agent, the **most critical task** is to clearly define and manage the **audio stream path between Asterisk and the AI processing modules**. This includes capturing inbound voice (from caller to ASR), generating responses (from LLM to TTS), and streaming synthesized audio back to the caller.

Understanding how audio flows **in and out of Asterisk**, and how it integrates with components like **ASR**, **TTS**, **LLM**, **AMD**, and **Turn Detection**, is essential for achieving low latency, scalability, and natural conversation quality.

![AI Agent Architecture](ai_agent.png)

---

## 🔧 Architecture of an AI Voice Agent

A complete AI Voice Agent typically consists of:

1. **AMD (Answering Machine Detection)** – Filters out voicemails to save processing resources.
2. **Turn Detection** – Determines when the caller has paused speaking before triggering the AI’s response.
3. **ASR (Automatic Speech Recognition)** – Converts caller audio to text.
4. **LLM (Large Language Model or NLU engine)** – Understands intent and generates replies.  
   ✅ *On-premise option:* Use **Rasa** as a local NLU engine to avoid sending data to cloud-based models.
5. **TTS (Text-to-Speech)** – Converts AI-generated text back to speech for playback.

To enable this pipeline, the Asterisk system must provide real-time access to the audio stream (via WebSocket, SIPREC, or file), and be able to play back synthesized audio from the TTS module.

---

## 🔄 5 Methods to Extract or Analyze Audio from Asterisk

### 1. AGI (Asterisk Gateway Interface)

AGI scripts allow you to record short user responses or prompts and pass them to ASR for interpretation.

- **Pros:** Simple, no external API needed  
- **Cons:** Not real-time, limited to synchronous prompts

---

### 2. ARI (Asterisk REST Interface)

ARI provides low-level control over media and channels, including real-time audio streaming via WebSocket. You can stream audio directly to ASR and receive transcripts instantly, then play back TTS audio responses dynamically.

- **Best for:** Real-time voicebot interaction  
- **Can integrate with:** ASR (e.g., Vosk, DeepSpeech), LLM (e.g., Rasa NLU), TTS (e.g., Coqui, custom)

---

### 3. Record to File (MixMonitor / Monitor)

This saves the entire call to a file for offline processing. You can analyze it later using ASR and LLM to generate summaries, QA reports, or training data.

- **Use case:** Post-call analysis, training AI models  
- **Drawback:** No real-time interaction

---

### 4. SIPREC (SIP Recording Protocol)

SIPREC duplicates the media stream to an external recording/processing server in real-time. Perfect for passive ASR and analytics without interrupting the primary call.

- **Scalable:** Ideal for high-volume call centers  
- **Can route to:** ASR pipeline, custom RTP server, or media proxy

---

### 5. AMD (Built-in Asterisk Feature)

Asterisk’s AMD module detects whether the answering party is a human or an answering machine by analyzing early audio patterns. This avoids unnecessary ASR usage for voicemails.

- **Reference:** [github.com/habachduong/asterisk_AMD](https://github.com/habachduong/asterisk_AMD)

---

## 🎧 Turn Detection (VAD-based)

Turn detection helps identify when the speaker has finished talking, allowing the AI to respond naturally without interruptions.

This can be implemented using **Voice Activity Detection (VAD)**, similar to LiveKit. A simple energy-based or WebRTC-style VAD engine segments audio into voice/silence and signals when to process the transcript.

- **Reference:** [github.com/habachduong/asterisk_VAD](https://github.com/habachduong/asterisk_VAD)  
- **Benefit:** Avoids talking over user, improves real-time flow  
- **Tools:** WebRTC VAD, Silero VAD, or energy thresholding

---

## 🗣️ ASR (Speech-to-Text Engine)

The ASR component is responsible for converting speech into structured text, which will be used as input for NLU/LLM. You can use cloud ASR (e.g., Google, Azure) or on-prem models like Vosk, DeepSpeech, or Whisper.

- **Reference:** [github.com/habachduong/asterisk_asr_vietnamese](https://github.com/habachduong/asterisk_asr_vietnamese)  
- **Languages supported:** Vietnamese, English  
- **Integration type:** Real-time or batch mode via WebSocket or file

---

## 🔊 TTS (Text-to-Speech Engine)

TTS is responsible for converting AI-generated responses into spoken audio for the caller. You can use cloud engines like Google TTS, or self-hosted TTS services for on-premise deployment.

- **Reference:** [github.com/habachduong/asterisk_tts_vietnamese](https://github.com/habachduong/asterisk_tts_vietnamese)  
- **Supported languages:** Vietnamese  
- **Integration options:** HTTP API, local playback via Asterisk ARI or AGI

---

## 🧠 Example: On-Prem AI Agent Stack

For a privacy-focused or air-gapped deployment, your full AI stack may look like:

- **Asterisk** – PBX and audio router  
- **ARI** – Audio streaming API  
- **Vosk / DeepSpeech / Whisper** – ASR engine (on-prem)  
- **Rasa NLU/Core** – Intent detection and dialogue manager (on-prem)  
- **Coqui TTS / asterisk_tts_vietnamese** – TTS engine (on-prem)  
- **Turn Detection** – VAD-based logic to detect speaker pause  
- **AMD** – Voicemail filtering

---

## ✅ Conclusion

Whether you're deploying AI voice agents on cloud or on-premise, Asterisk provides multiple paths to integrate with ASR and LLM components. By combining:

- **AGI or ARI** for audio handling  
- **SIPREC** for stream duplication  
- **AMD** for intelligent filtering  
- **VAD** for natural turn-taking  
- **Custom ASR and TTS** for Vietnamese language  
- **Rasa** for on-premise NLU

...and most importantly, by correctly routing the **audio stream from Asterisk into and out of the AI pipeline**, you can build scalable, real-time voice AI systems that respect privacy, work in local environments, and provide intelligent human-like interactions over the phone.
