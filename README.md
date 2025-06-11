# Micro-IT-internship-project-1
# actions.py
from typing import Any, Text, Dict, List
from rasa_sdk import Action, Tracker
from rasa_sdk.executor import CollectingDispatcher

class ActionDefaultFallback(Action):
    def name(self) -> Text:
        return "action_default_fallback"

    def run(self, dispatcher: CollectingDispatcher, tracker: Tracker, domain: Dict[Text, Any]) -> List[Dict[Text, Any]]:
        dispatcher.utter_message(text="Sorry, I didn't understand that. Could you please rephrase?")
        return []

# data.yml
version: "3.1"
nlu:
- intent: greet
  examples: |
    - hi
    - hello
    - hey
    - good morning

- intent: goodbye
  examples: |
    - bye
    - goodbye
    - see you

- intent: ask_internship_duration
  examples: |
    - How long is the internship?
    - What is the internship duration?

- intent: ask_application_process
  examples: |
    - How can I apply?
    - What is the application process?
    - Where should I apply for the internship?

- intent: ask_organization_info
  examples: |
    - Tell me about the organization
    - What does your organization do?

# stories.yml
version: "3.1"
stories:
- story: greet and ask internship
  steps:
  - intent: greet
  - action: utter_greet
  - intent: ask_internship_duration
  - action: utter_internship_duration

# rules.yml
version: "3.1"
rules:
- rule: Say goodbye
  steps:
  - intent: goodbye
  - action: utter_goodbye

# domain.yml
version: "3.1"

intents:
  - greet
  - goodbye
  - ask_internship_duration
  - ask_application_process
  - ask_organization_info

responses:
  utter_greet:
    - text: "Hello! How can I assist you today?"
  utter_goodbye:
    - text: "Goodbye! Have a great day."
  utter_internship_duration:
    - text: "Our internships typically last 8-12 weeks."
  utter_application_process:
    - text: "You can apply through our Careers page at www.example.com/internships."
  utter_organization_info:
    - text: "We are a leading organization offering hands-on experience in tech and management fields."

actions:
  - action_default_fallback

session_config:
  session_expiration_time: 60
  carry_over_slots_to_new_session: true

# config.yml

language: en
pipeline:
  - name: WhitespaceTokenizer
  - name: RegexFeaturizer
  - name: LexicalSyntacticFeaturizer
  - name: CountVectorsFeaturizer
  - name: DIETClassifier
    epochs: 100
  - name: FallbackClassifier
    threshold: 0.3
    ambiguity_threshold: 0.1

policies:
  - name: RulePolicy

# credentials.yml
rest:

# endpoints.yml
action_endpoint:
  url: "http://localhost:5055/webhook"
