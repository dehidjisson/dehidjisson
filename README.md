import datetime
import random
from telegram import Update, Bot
from telegram.ext import Updater, CommandHandler, CallbackContext

# Remplacez 'YOUR_TOKEN_HERE' par le token fourni par BotFather
TELEGRAM_TOKEN = '7142520461:AAFtuJJEyRwmbP0Dt_lFR9M73I_gbrL2x0A'

def obtenir_heure_actuelle():
    """Récupérer l'heure actuelle."""
    try:
        return datetime.datetime.now()
    except Exception as e:
        print(f"Erreur lors de la récupération de l'heure actuelle : {e}")
        return None

def generer_cotes(hack, maximum):
    """Générer des cotes aléatoires dans une plage donnée."""
    try:
        if hack >= maximum:
            raise ValueError("La valeur de hack doit être inférieure à la valeur maximale.")

        coefficient_number = round(random.uniform(hack, maximum), 2)
        half_number = round(coefficient_number / 2, 2)
        fiabilite = round(half_number / 2, 2)
        return coefficient_number, half_number, fiabilite
    except Exception as e:
        print(f"Erreur lors de la génération des cotes : {e}")
        return None, None, None

def afficher_cotes(heure_date, coefficient_number, half_number, fiabilite):
    """Formater et afficher les cotes avec l'heure donnée."""
    heure_str = str(heure_date.hour).zfill(2)
    minute_str = str(heure_date.minute).zfill(2)
    seconde_str = str(heure_date.second).zfill(2)

    heure_str_combinee = f"{heure_str}:{minute_str}-{seconde_str}"

    message = (
        "\n🚀 *NOUVEAU SIGNAL* 💸\n\n"
        f"Heure: {heure_str_combinee}\n"
        f"Côte : {coefficient_number}\n"
        f"Assurance : {half_number}\n"
        f"Fiabilité : {fiabilite}\n"
        "\n*OLIVIERPCS* 🐉💰\n"
    )
    return message

def start(update: Update, context: CallbackContext):
    hack = 7.00
    maximum = 10.00
    minutes_avancees = 4

    heure_date = obtenir_heure_actuelle()

    if heure_date:
        heure_date = heure_date + datetime.timedelta(minutes=minutes_avancees)

        messages = []
        for _ in range(3):  # Afficher pour les 3 prochaines périodes de temps
            coefficient_number, half_number, fiabilite = generer_cotes(hack, maximum)
            if coefficient_number is not None:
                message = afficher_cotes(heure_date, coefficient_number, half_number, fiabilite)
                messages.append(message)
                heure_date += datetime.timedelta(minutes=minutes_avancees)

        for msg in messages:
            update.message.reply_text(msg, parse_mode='Markdown')

def main():
    updater = Updater(TELEGRAM_TOKEN, use_context=True)

    dp = updater.dispatcher
    dp.add_handler(CommandHandler("start", start))

    updater.start_polling()
    updater.idle()

if __name__ == "__main__":
    main()
