import ask_sdk_core.utils as ask_utils
from ask_sdk_core.skill_builder import SkillBuilder
from ask_sdk_core.dispatch_components import AbstractRequestHandler
from ask_sdk_core.handler_input import HandlerInput
from ask_sdk_model import Response
from ask_sdk_model.services import ReminderManagementServiceClient
from ask_sdk_model.services.reminder_management import Trigger, TriggerType, RecurrenceFreq, Recurrence
from datetime import datetime, timedelta

class LaunchRequestHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        return ask_utils.is_request_type("LaunchRequest")(handler_input)

    def handle(self, handler_input):
        speak_output = "Welcome to Water Reminder. When would you like to start the reminders?"
        return (
            handler_input.response_builder
                .speak(speak_output)
                .ask(speak_output)
                .response
        )


class SetReminderIntentHandler(AbstractRequestHandler):
    def can_handle(self, handler_input):
        return ask_utils.is_intent_name("SetReminderIntent")(handler_input)

    def handle(self, handler_input):
        start_date = handler_input.request_envelope.request.intent.slots["date"].value
        start_date = datetime.strptime(start_date, "%Y-%m-%d")

        reminder_client = handler_input.service_client_factory.get_reminder_management_service()
        trigger = Trigger(
            type=TriggerType.SCHEDULED_ABSOLUTE,
            scheduled_time=start_date.isoformat(),
            recurrence=Recurrence(freq=RecurrenceFreq.DAILY, interval=2)
        )

        reminder_request = {
            "trigger": trigger,
            "alert_info": {
                "spokenInfo": {
                    "content": [{
                        "locale": "en-US",
                        "text": "It's time for your water reminder."
                    }]
                }
            },
            "push_notification": {
                "status": "ENABLED"
            }
        }

        reminder_client.create_reminder(reminder_request)

        speak_output = f"I've set a water reminder for every odd day, beginning on {start_date.strftime('%B %d, %Y')}."
        return (
            handler_input.response_builder
                .speak(speak_output)
                .response
        )

sb = SkillBuilder()

sb.add_request_handler(LaunchRequestHandler())
sb.add_request_handler(SetReminderIntentHandler())

handler = sb.lambda_handler()
