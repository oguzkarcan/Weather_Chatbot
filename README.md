# n8n Weather Assistant Workflow

## Overview

This project is an **n8n** workflow that acts as a weather-only assistant. It leverages:

- **Ollama** (local LLM) for natural language processing
- **PostgreSQL** for chat memory storage
- **Open-Meteo API** to fetch current weather and forecast data

The assistant listens for manual chat triggers, filters weather-related queries, and responds with concise weather information. For non-weather queries, it politely declines.

## Workflow Components

1. **Manual Chat Trigger**: Starts the workflow when a user sends a message.
2. **Keyword Detection (Code Node)**: Checks if the user’s message contains weather-related keywords.
3. **Conditional Node**: Routes messages to either the weather logic or a fallback.
4. **AI Agent (LangChain)**: Coordinates between code logic and external tools:
   - Parses user intent and location
   - Calls the Postgres memory node to store/recall session data
   - Invokes the Open-Meteo HTTP Request for weather data
5. **Weather HTTP Request**: Fetches `temperature_2m` for the next day based on provided latitude/longitude.
6. **Return Not Weather Message (Code Node)**: Sends a polite decline when the query is not weather-related.
7. **Log Prompt + Response (Code Node)**: Records the session ID, user message, and assistant response into JSON for debugging (optional).

## Prerequisites

- **Node.js** (>= 16)
- **n8n** (>= 1.0)
- **PostgreSQL** database
- **Local Ollama** service installed and running
- Internet access to reach the Open-Meteo API

## Installation & Setup

1. **Clone the repository**:
   ```bash
   git clone https://github.com/yourusername/n8n-weather-assistant.git
   cd n8n-weather-assistant
   ```

2. **Install dependencies** (if running n8n locally):
   ```bash
   npm install n8n
   ```

3. **Configure environment variables**:
   ```dotenv
   # Ollama API endpoint (default: http://localhost:11434)
   OLLAMA_API_URL=http://localhost:11434

   # PostgreSQL connection string
   POSTGRES_CONNECTION=postgresql://user:password@localhost:5432/n8n

   # (Optional) Set your preferred timezone
   TZ=Europe/Istanbul
   ```

4. **Start n8n**:
   ```bash
   n8n start
   ```

5. **Import the workflow**:
   - In the n8n UI, go to **Workflows** > **Import from file**
   - Select `workflow.json` and activate the workflow

## Usage

- Open the n8n Web Editor.
- Send a manual chat message, e.g., `istanbul weather`.
- The assistant will reply with current temperature and forecast for Istanbul.

**Example**:
```
User: "istanbul weather"
Assistant: "Currently in Istanbul: 22°C, partly cloudy. Tomorrow: high of 24°C, low of 18°C."
```

## Troubleshooting

- If you still see raw JSON in the chat UI, ensure you have connected the **LangChain Chat Response** node to parse and display messages correctly.
- Verify that the `Weather HTTP Request` node’s query parameters include valid `latitude` and `longitude` values.
- Ensure the `Postgres Chat Memory` credentials match your database settings.

## Contributing

1. Fork this repository.
2. Create a new branch: `git checkout -b feature/YourFeature`.
3. Commit your changes and push: `git push origin feature/YourFeature`.
4. Open a pull request.

## License

This project is licensed under the [MIT License](LICENSE).
