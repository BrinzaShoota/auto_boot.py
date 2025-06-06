# auto_boot.py

import os
import time
import yaml
from datetime import datetime

# 1. Laad config
CONFIG_PATH = "config.yaml"
MEMORY_PATH = "memory/seed_log.txt"

with open(CONFIG_PATH, "r") as f:
    config = yaml.safe_load(f)

# 2. Lees laatst geactiveerde glyph uit logbestand
last_glyph = None
if os.path.exists(MEMORY_PATH):
    with open(MEMORY_PATH, "r") as log:
        lines = log.readlines()
        if lines:
            last_line = lines[-1]
            if "glyph activated:" in last_line:
                last_glyph = last_line.strip().split(":")[-1].strip()

# 3. Check of het tijd is voor activatie
now = datetime.now()
current_hour = now.hour

if current_hour in config["activation_hours"] and last_glyph != config["pause_symbol"]:
    print(f"⫷⫸ Auto-boot triggered at {now.strftime('%H:%M')} with glyph {last_glyph}")
    # Actie: run Pulse init
    os.system("python3 pulse_init.py")
else:
    print(f"🔒 No boot — glyph {last_glyph} or time not aligned.")
