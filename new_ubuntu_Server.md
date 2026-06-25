# CLAUDE.md - Contexto Completo del Proyecto Palcos y Cifras

**Última actualización**: Junio 2025  
**Autor**: Pere (IT Professional, Spain)  
**Proyecto**: Palcos y Cifras - YouTube channel (Sports Economics)  
**Ubicación**: Ubuntu Server 24 + Tailscale SSH  
**Repositorio**: GitHub (palcos-proyecto)

---

## 🎯 MISIÓN DEL PROYECTO

Crear un sistema **100% automatizado** que genere episodios de YouTube de alta calidad sobre economía del deporte (Palcos y Cifras). 

**Stack actual:**
- Claude API (IA principal para scripts)
- Whisper (transcripción local)
- Google TTS (voiceover profesional)
- ffmpeg (audio processing)
- Remotion (video rendering - TODO)

---

## 📋 CONTEXTO CRÍTICO

### Hardware
```
Ubuntu Server 24
├─ RTX 3050 (6GB VRAM)
├─ Ryzen 5 3600 (4 CPUs)
├─ 6GB RAM
└─ Acceso via SSH + Tailscale
```

### El Usuario
- **Pere**: IT Professional, basado en Cataluña, Spain
- Desarrollo de contenido de sports economics en YouTube
- Experiencia: Python, Bash, Git, Claude Pro
- Otros proyectos: Nexara (web agency), technical auditing

### Requisitos Críticos
1. ✅ **TODO corre en Ubuntu Server** (sin dependencia de Mac)
2. ✅ **Sincronización GitHub** (código siempre en sync)
3. ✅ **Máxima calidad** (Claude API, no Ollama fallback)
4. ✅ **Acceso remoto** (SSH Tailscale desde cualquier lado)
5. ✅ **Costo controlado** (Claude Pro + API tokens, ~€50-70/mes)
6. ✅ **Ilimitado** (generar 100+ episodios/mes)
7. ✅ **100% Automatizable** (cron jobs, fire-and-forget)

---

## 🏗️ ARQUITECTURA COMPLETA

```
┌─────────────────────────────────────────────────────────────────┐
│ GITHUB (Repositorio Central)                                    │
│ palcos-proyecto/                                                │
│ ├─ scripts/                                                     │
│ │  ├─ generate_episode.py      (Claude API → Script JSON)     │
│ │  ├─ generate_audio.py         (Google TTS → MP3)            │
│ │  ├─ transcribe.py             (Whisper → Transcript)        │
│ │  ├─ orchestrator.py           (Coordina todo)               │
│ │  └─ requirements.txt                                        │
│ ├─ config/                                                     │
│ │  ├─ prompts.yaml              (Prompts de Claude)           │
│ │  └─ settings.yaml             (Configuración)               │
│ ├─ CLAUDE.md                    (Este archivo)                │
│ ├─ README.md                                                  │
│ └─ .gitignore                   (Incluye .env)                │
└─────────────────────────────────────────────────────────────────┘
                           ↓ (git clone/pull)
┌─────────────────────────────────────────────────────────────────┐
│ UBUNTU SERVER 24 (Máquina Principal)                           │
│                                                                  │
│ ~/palcos-proyecto/ (clonado de GitHub)                        │
│ ├─ venv/                       (Python environment)            │
│ ├─ .env                        (SECRETOS, NO en GitHub)       │
│ ├─ scripts/                    (del GitHub)                    │
│ │  └─ orchestrator.py          (ESTO es lo que usas)         │
│ └─ data/                                                       │
│    ├─ scripts/                 (JSON generados por Claude)    │
│    ├─ audios/                  (MP3s generados)               │
│    ├─ videos/                  (MP4s - Remotion)              │
│    └─ logs/                    (Logs de ejecución)            │
│                                                                  │
│ Servicios:                                                     │
│ ├─ Claude API                  (Integrada en scripts)         │
│ ├─ Whisper                     (Servicio local)               │
│ ├─ Google TTS API              (Integrada en scripts)         │
│ ├─ ffmpeg                      (Disponible en CLI)            │
│ └─ Cron jobs                   (Automatización)               │
│                                                                  │
│ Acceso: ssh usuario@100.64.x.x (Tailscale)                   │
└─────────────────────────────────────────────────────────────────┘

Flujo típico:
1. ssh a Ubuntu
2. git pull (traer últimos cambios)
3. python3 scripts/orchestrator.py --title "..." --topic "..." --duration 180
4. 1-2 minutos después: episodio completo
```

---

## 🛠️ STACK TECNOLÓGICO

| Componente | Ubicación | Función | Costo |
|-----------|-----------|---------|-------|
| **Claude API** | Ubuntu (scripts) | Generar scripts económicos | ~€0.03-0.05 por episodio |
| **Whisper** | Ubuntu (local) | Transcribir audio | €0 (local) |
| **Google TTS** | Ubuntu (API) | Generar voiceover | €0 (gratuito tier) |
| **ffmpeg** | Ubuntu (CLI) | Procesar audio/video | €0 (open source) |
| **Remotion** | Ubuntu (Node.js) | Renderizar video | €0 (open source) |
| **GitHub** | Cloud | Sincronización código | €0 (free tier) |
| **PostgreSQL** | Ubuntu (optional) | Cache de datos | €0 (local) |

---

## 📁 ESTRUCTURA DE ARCHIVOS

### scripts/
```python
generate_episode.py
├─ Función: generate_script(title, topic, duration, economic_data)
├─ Usa: Claude API
├─ Input: título, tema, duración, datos económicos
├─ Output: JSON con estructura de episodio
├─ Logging: INFO + ERROR
└─ Args: --title, --topic, --duration, --economic-data

generate_audio.py
├─ Función: generate_audio(text, output_path, voice, language)
├─ Usa: Google Cloud TTS
├─ Input: texto a leer, ruta output
├─ Output: MP3 profesional
└─ Args: texto, output.mp3, [voice], [idioma]

transcribe.py
├─ Función: transcribe(audio_path, language)
├─ Usa: Whisper (local, GPU)
├─ Input: archivo audio
├─ Output: JSON con transcripción
└─ Args: audio.wav, [idioma]

orchestrator.py (PRINCIPAL)
├─ Coordina: generate_episode → generate_audio → transcribe
├─ Logging: Detallado, con timestamps
├─ Output: Episodio completo (script + audio + transcript)
├─ Error handling: Robusto, rollback si falla
└─ Args: --title, --topic, --duration
```

### config/
```yaml
prompts.yaml
├─ system_prompt_script: "Eres experto en economía del deporte..."
├─ prompt_template_script: "Genera script para video de {duration}s..."
├─ voices_available: [es-ES-Neural2-A, es-ES-Neural2-C, ...]
├─ whisper_config: {language: es, model: large, device: cuda}
└─ orchestrator_config: {timeout: 300, retries: 3, ...}

settings.yaml
├─ Claude:
│  ├─ model: claude-3-5-sonnet-20241022
│  ├─ max_tokens: 2000
│  ├─ temperature: 0.7
│  └─ timeout: 300s
├─ GoogleTTS:
│  ├─ default_voice: es-ES-Neural2-C
│  ├─ language: es-ES
│  └─ rate_limit: 1 request/second
├─ Whisper:
│  ├─ model: large
│  ├─ device: cuda
│  ├─ compute_type: float16
│  └─ language: es
└─ Paths:
   ├─ data_dir: ./data
   ├─ logs_dir: ./data/logs
   └─ scripts_dir: ./data/scripts
```

---

## 🔑 VARIABLES DE ENTORNO (.env)

**NO COMMITTEAR A GITHUB** - Sólo en Ubuntu Server

```bash
# Claude API
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxxxxxxxxx

# Google Cloud TTS
GOOGLE_APPLICATION_CREDENTIALS=/home/usuario/palcos-proyecto/.google/credentials.json

# Configuración
PALCOS_MODEL=claude-3-5-sonnet-20241022
PALCOS_DATA_DIR=/home/usuario/palcos-proyecto/data
PALCOS_LOGS_DIR=/home/usuario/palcos-proyecto/data/logs

# Optional
DEBUG=false
LOG_LEVEL=INFO
```

---

## 💻 CÓMO USAR (Para Claude Code)

### Setup Inicial (Primera vez)

```bash
# En Ubuntu Server
ssh usuario@100.64.x.x
cd ~/palcos-proyecto

# Clonar/Pull desde GitHub
git clone https://github.com/tu-user/palcos-proyecto.git
cd palcos-proyecto

# Crear venv
python3.11 -m venv venv
source venv/bin/activate

# Instalar dependencias
pip install -r requirements.txt

# Configurar .env
cp .env.example .env
# Editar .env con tus API keys
```

### Uso Normal

```bash
# SSH a Ubuntu
ssh usuario@100.64.x.x

# Traer últimos cambios
cd ~/palcos-proyecto
git pull

# Activar venv
source venv/bin/activate

# Generar episodio
python3 scripts/orchestrator.py \
  --title "Salarios Liga EA 2024" \
  --topic "Evolución de salarios en La Liga Española" \
  --duration 180

# Output: JSON + MP3 + Transcript generados en data/
```

### Desarrollo (Mejoras de código)

```bash
# Editar scripts
vim scripts/generate_episode.py

# Test local
python3 scripts/generate_episode.py --title "Test" --topic "Test" --duration 60

# Si funciona, commit
git add .
git commit -m "Mejora: [descripción]"
git push

# En Ubuntu (o cualquier otra máquina)
git pull
# Cambios activos automáticamente
```

---

## 📊 FLUJOS DE TRABAJO

### Flujo 1: Generar episodio manual
```
ssh → git pull → python3 orchestrator.py → Episodio listo en 1-2 min
```

### Flujo 2: Generar episodios automáticos (cron)
```
crontab -e
0 10 * * TUE python3 ~/palcos-proyecto/scripts/orchestrator.py ...
# Cada martes a las 10 AM: episodio automático
```

### Flujo 3: Iterar/mejorar código
```
Editar scripts localmente (Mac/PC)
→ git commit + push
→ ssh a Ubuntu
→ git pull
→ Test
→ En producción
```

---

## 🎬 EJEMPLO: Crear un Episodio Completo

```bash
# 1. SSH a Ubuntu
ssh usuario@100.64.x.x

# 2. Ir al proyecto
cd ~/palcos-proyecto
source venv/bin/activate

# 3. Ejecutar orquestador
python3 scripts/orchestrator.py \
  --title "Deuda de clubes españoles 2024" \
  --topic "Cómo la deuda afecta a los salarios en La Liga" \
  --duration 240 \
  --economic-data '{"total_debt": 3200000000, "currency": "EUR", "source": "LFP 2024"}'

# Output esperado:
# ════════════════════════════════════════════════════════
# 🎬 GENERANDO EPISODIO: Deuda de clubes españoles 2024
# 📝 PASO 1: Generando script con Claude API...
# ✅ Script guardado: data/scripts/20240625_143045_script.json
# 🎙️  PASO 2: Generando audio con Google TTS...
# ✅ Audio guardado: data/audios/20240625_143045_voiceover.mp3
# 📝 PASO 3: Transcribiendo audio...
# ✅ Transcripción: data/scripts/20240625_143045_transcript.json
# 
# ╔════════════════════════════════════════════════════════╗
# ║       ✅ EPISODIO GENERADO CON ÉXITO                  ║
# ║  Tiempo: 1-2 minutos                                  ║
# ║  Costo: ~€0.05-0.10 (tokens Claude)                  ║
# ║  Calidad: 9.5/10                                     ║
# ╚════════════════════════════════════════════════════════╝
```

---

## 🚨 TROUBLESHOOTING RÁPIDO

### Error: "ANTHROPIC_API_KEY not found"
```bash
# Solución
vim .env
# Agregar tu API key
export ANTHROPIC_API_KEY="sk-ant-xxx"
```

### Error: "Conexión a Google TTS fallida"
```bash
# Solución
# Verificar credentials.json está en .google/
ls -la .google/credentials.json

# Si no existe, descargar desde Google Cloud Console
# y copiar a .google/credentials.json
```

### Error: "GPU VRAM insuficiente"
```bash
# Solución
# Cambiar en settings.yaml:
# compute_type: float32 → float16 (usa menos VRAM)
```

### Error: "Timeout generando script"
```bash
# Solución
# Aumentar timeout en settings.yaml:
# timeout: 300 → 600 (10 minutos)
```

---

## 📈 ROADMAP DEL PROYECTO

### ✅ Completado
- [x] Claude API integrada en Ubuntu
- [x] Google TTS para voiceover
- [x] Whisper para transcripción
- [x] Orchestrator principal
- [x] GitHub synchronization
- [x] Logging y monitoring
- [x] Manejo de errores robusto

### 🔄 En Progreso
- [ ] Integración Remotion (video rendering)
- [ ] ffmpeg post-processing
- [ ] Caché de episodios (PostgreSQL)
- [ ] Dashboard web (opcional)

### 📋 TODO
- [ ] YouTube API integration (upload automático)
- [ ] TikTok export (formato vertical)
- [ ] Kids4Fun26 (canal infantil)
- [ ] Análisis de rendimiento (views, engagement)
- [ ] A/B testing de thumbnails
- [ ] Análisis de comentarios (sentiment)

---

## 📞 CONTACTO / REFERENCIAS

### APIs Utilizadas
- **Claude API**: https://console.anthropic.com
- **Google Cloud TTS**: https://cloud.google.com/text-to-speech
- **OpenAI Whisper**: https://github.com/openai/whisper
- **Remotion**: https://www.remotion.dev

### Documentación
- Claude: https://docs.claude.com
- Google TTS: https://cloud.google.com/text-to-speech/docs
- Whisper: https://github.com/openai/whisper
- Remotion: https://www.remotion.dev/docs

### Hardware / Infraestructura
- Ubuntu Server: 24.04 LTS
- GPU: NVIDIA RTX 3050 (6GB VRAM)
- CPU: AMD Ryzen 5 3600 (4 cores)
- RAM: 6GB
- Acceso: SSH via Tailscale VPN

---

## 💰 COSTOS MENSUALES

```
Claude Pro              €20/mes (acceso web)
Claude API tokens       €30-50/mes (100+ episodios)
Whisper (local)         €0
Google TTS (gratuito)   €0
GitHub (free)           €0
Infraestructura         €0 (lo tienes)

TOTAL:                  €50-70/mes
```

**Por episodio:** ~€0.05-0.10

---

## 🎯 INSTRUCCIONES PARA CLAUDE CODE

1. **Lee completamente este archivo** para entender el contexto
2. **Revisa la estructura en GitHub**: palcos-proyecto/
3. **Comprende el flujo**: orchestrator.py coordina todo
4. **Cuando hagas cambios:**
   - Edita scripts/ o config/
   - Test local antes de commit
   - Commit a GitHub
   - Síncroniza en Ubuntu
5. **Prioridades:**
   - ✅ Robustez (manejo de errores)
   - ✅ Logging detallado
   - ✅ Reproducibilidad
   - ✅ Documentación inline

---

## 📝 CONVENCIONES DE CÓDIGO

```python
# Naming
generate_episode_by_claude()  # Función
ANTHROPIC_API_KEY             # Constante
episode_data                   # Variable

# Logging
logger.info("📝 Generando script...")     # Información
logger.warning("⚠️  Reintentando...")     # Advertencia
logger.error("❌ Error crítico")          # Error

# Error handling
try:
    result = claude_api_call()
except anthropic.APIError as e:
    logger.error(f"❌ Claude API: {e}")
    raise

# Docstrings
def generate_script(title: str, topic: str, duration: int) -> dict:
    """
    Generar script económico usando Claude API.
    
    Args:
        title: Título del episodio
        topic: Tema económico
        duration: Duración en segundos
    
    Returns:
        dict: Script JSON con estructura del video
    
    Raises:
        anthropic.APIError: Si falla la llamada a Claude
    """
```

---

## ✨ ÚLTIMO CONSEJO

**Cuando trabajes desde Claude Code:**
1. Ten este archivo abierto como referencia
2. Revisa los scripts existentes antes de modificar
3. Test siempre localmente antes de commit
4. Mantén el logging informativo (ayuda a debuggear)
5. Comenta cambios significativos
6. Sigue las convenciones de código

**El objetivo:** Palcos y Cifras 100% automatizado, generando contenido de alta calidad sin intervención manual.

---

**Última actualización**: Junio 2025  
**Status**: Activo, en desarrollo continuo  
**Próxima revisión**: Cuando integres Remotion
