#CookieOS
#load into python 3.0 or above

import os
import sys
import random
import time

# Try to enable colored output; fallback if unavailable
try:
    from colorama import init, Fore, Style
    init(autoreset=True)
    C_OK   = Fore.GREEN
    C_WARN = Fore.YELLOW
    C_ERR  = Fore.RED
    C_DIM  = Style.DIM
    C_RST  = Style.RESET_ALL
except ImportError:
    C_OK = C_WARN = C_ERR = C_DIM = C_RST = ""

# Global Settings
settings = {
    "Frosting Mode": True,
    "Crumb Cleanup": False,
    "Auto-Bake at Startup": True
}

# Ensure a demo cookie_drive folder exists
DRIVE = "cookie_drive"
DEMO_FILES = {
    "frosting_log.txt": "01/01/2025 – Frosting yields up by 12%\n02/14/2025 – Heart-shaped cookies success!",
    "grandma_secrets.md": "# Grandma's Secret Recipes\n- Oatmeal Raisin Deluxe\n- Triple Chocolate Bomb",
    "README.crunch": "Welcome to your Cookie Drive! 🍪\nUse the File Explorer to read files."
}
if not os.path.isdir(DRIVE):
    os.makedirs(DRIVE, exist_ok=True)
    for name, content in DEMO_FILES.items():
        with open(os.path.join(DRIVE, name), "w") as f:
            f.write(content)

# --------------------
# 1. File Explorer
# --------------------
def file_explorer():
    print(f"\n{C_OK}🍪 File Explorer{C_RST}")
    files = os.listdir(DRIVE)
    if not files:
        print("  (cookie_drive is empty)")
    else:
        for i, fname in enumerate(files, 1):
            mark = "💾" if settings["Frosting Mode"] else ""
            print(f"  {i}. {mark}{fname}")
        choice = input("\nEnter number to view file, or press Enter to go back: ").strip()
        if choice.isdigit() and 1 <= int(choice) <= len(files):
            fname = files[int(choice) - 1]
            path = os.path.join(DRIVE, fname)
            print(f"\n--- {fname} ---")
            with open(path) as f:
                print(f.read())
            print("--- End of file ---")
    input("\nPress Enter to return to menu...")

# --------------------
# 2. CookieNet Explorer
# --------------------
def cookienet():
    print(f"\n{C_OK}🍪🌐 CookieNet Explorer v2.0{C_RST}")
    print("Type a URL (e.g. Welp.net  📢) or 'back'")
    while True:
        url = input("URL> ").strip()
        if url.lower() == "back":
            break
        # Easter eggs
        if "cookiepedia" in url:
            print("🍪 You're the encyclo-snacker! 🍪")
        elif "Welp" in url:
            print("welcome to welp! loading latest reviews...")
            time.sleep(2)
            print("done!")
            print("⭐⭐ Taco bell? more like fart bell!")
            print("i got a burrito and farted! 😤 burrito was tasty though.")
            print("--- end of review ---")
            print("⭐⭐⭐⭐⭐ 🚩this sucks!")
            print("skibidi rizzler dentistry sucks because of name.")
            print("this review was flagged cuz SUS")
            print("--- end of review ---")
            print("⭐ WHAT YOU IDIOTIC SIMPOLTIN?!")
            print("they called me ugly")
            print("comment: i saw your blog photo and you are. haha!")
            print("--- end of review ---")
        elif "bonzi" in url:
            print("downloading something...")
            time.sleep(5)
            print("bonzi infected your computer.")
            sys.exit()
        else:
            print(f"Loading {url}... Done! [Sweet browsing]")
    input("\nPress Enter to return to menu...")

# --------------------
# 3. Control Panel
# --------------------
def control_panel():
    print(f"\n{C_OK}🍪 Control Panel{C_RST}")
    opts = list(settings.items())
    for i, (key, val) in enumerate(opts, 1):
        state = C_OK + "On " if val else C_WARN + "Off"
        print(f"  {i}. {C_RST}{key}: {state}")
    choice = input("\nEnter number to toggle, or press Enter to go back: ").strip()
    if choice.isdigit() and 1 <= int(choice) <= len(opts):
        key = opts[int(choice)-1][0]
        settings[key] = not settings[key]
        print(f"{key} set to {'On' if settings[key] else 'Off'}")
    input("\nPress Enter to return to menu...")

# --------------------
# 4. Multiplayer Minesweeper (5x5)
# --------------------
def minesweeper():
    size, mines = 5, 5
    coords = [(r, c) for r in range(size) for c in range(size)]
    mine_positions = set(random.sample(coords, mines))
    revealed = set()
    players = ["Player 1", "Player 2"]
    turn = 0

    def print_board(show_mines=False):
        print("\n   " + " ".join(str(c+1) for c in range(size)))
        for r in range(size):
            row = []
            for c in range(size):
                if (r, c) in revealed:
                    # count neighbors
                    cnt = sum((nr, nc) in mine_positions
                              for nr in range(r-1, r+2)
                              for nc in range(c-1, c+2)
                              if 0 <= nr < size and 0 <= nc < size)
                    row.append(str(cnt))
                elif show_mines and (r, c) in mine_positions:
                    row.append("M")
                else:
                    row.append("*")
            print(f"{r+1:2} " + " ".join(row))
        print()

    def all_safe_revealed():
        return len(revealed) == size*size - mines

    print(f"\n{C_OK}🍪 Multiplayer Minesweeper{C_RST}")
    while True:
        print_board()
        print(f"{players[turn]}'s turn.")
        choice = input("Enter row,col (e.g. 3,4) or 'quit': ").strip()
        if choice.lower() == "quit":
            break
        try:
            r, c = map(int, choice.split(","))
            r -= 1; c -= 1
            if not (0 <= r < size and 0 <= c < size):
                raise ValueError
            if (r, c) in revealed:
                print("Already revealed!")
                continue
        except:
            print("Invalid input.")
            continue

        if (r, c) in mine_positions:
            print_board(show_mines=True)
            print(f"{C_ERR}💥 {players[turn]} hit a mine! Game over.{C_RST}")
            print(f"{C_OK}{players[1-turn]} wins!{C_RST}")
            break

        revealed.add((r, c))
        if all_safe_revealed():
            print_board()
            print(f"{C_OK}🎉 All safe spots found! Both players win (crumb alliance)!{C_RST}")
            break
        turn = 1 - turn  # switch turn

    input("\nPress Enter to return to menu...")

# --------------------
# 5. Reversi (8x8)
# --------------------
def reversi():
    N = 8
    board = [[" "]*N for _ in range(N)]
    # Initial discs
    board[3][3] = board[4][4] = "O"
    board[3][4] = board[4][3] = "X"
    players = [("O", "Player 1"), ("X", "Player 2")]

    def print_board():
        print("\n   " + " ".join(str(i+1) for i in range(N)))
        for r in range(N):
            print(f"{r+1:2} " + " ".join(board[r]))
        print()

    directions = [(-1,-1),(-1,0),(-1,1),(0,-1),(0,1),(1,-1),(1,0),(1,1)]

    def valid_moves(player):
        opp = "X" if player == "O" else "O"
        moves = {}
        for r in range(N):
            for c in range(N):
                if board[r][c] != " ": continue
                flips = []
                for dr, dc in directions:
                    path = []
                    nr, nc = r+dr, c+dc
                    while 0<=nr<N and 0<=nc<N and board[nr][nc] == opp:
                        path.append((nr,nc))
                        nr += dr; nc += dc
                    if path and 0<=nr<N and 0<=nc<N and board[nr][nc]==player:
                        flips.extend(path)
                if flips:
                    moves[(r,c)] = flips
        return moves

    turn = 0
    print(f"\n{C_OK}🍪 Reversi (8x8){C_RST}")
    while True:
        pdisc, pname = players[turn]
        moves = valid_moves(pdisc)
        if not moves:
            # Check if opponent has moves
            if not valid_moves(players[1-turn][0]):
                break
            print(f"{pname} has no valid moves. Skipping turn.")
            turn = 1 - turn
            continue

        print_board()
        print(f"{pname} ({pdisc}) to move.")
        print(f"Valid moves: " + ", ".join(f"({r+1},{c+1})" for r,c in moves))
        choice = input("Enter row,col or 'quit': ").strip()
        if choice.lower() == "quit":
            break
        try:
            r, c = map(int, choice.split(","))
            key = (r-1, c-1)
            if key not in moves:
                raise ValueError
        except:
            print("Invalid move.")
            continue

        # Place and flip
        board[r-1][c-1] = pdisc
        for fr, fc in moves[key]:
            board[fr][fc] = pdisc
        turn = 1 - turn

    # Game over: count discs
    counts = {"O":0, "X":0}
    for row in board:
        for cell in row:
            if cell in counts:
                counts[cell] += 1
    print_board()
    print(f"Final Score – O: {counts['O']}, X: {counts['X']}")
    if counts["O"] > counts["X"]:
        print(f"{C_OK}Player 1 (O) wins!{C_RST}")
    elif counts["X"] > counts["O"]:
        print(f"{C_OK}Player 2 (X) wins!{C_RST}")
    else:
        print(f"{C_WARN}It's a tie!{C_RST}")

    input("\nPress Enter to return to menu...")

# --------------------
# 6. Adventure Mode RPG
# --------------------
def adventure():
    print(f"\n{C_OK}🍪 CookieQuest RPG{C_RST}")
    # World definition
    rooms = {
        "Entrance": {
            "desc": "A grand archway of sugar paste. Paths lead North and East.",
            "exits": {"north": "Hallway", "east": "Kitchen"},
            "items": []
        },
        "Hallway": {
            "desc": "Frosted portraits line the walls. A locked door lies North.",
            "exits": {"south": "Entrance", "north": "Vault"},
            "locked": {"north": "Fudge Key"},
            "items": ["Lore Scroll"]
        },
        "Kitchen": {
            "desc": "Steam rises from mixing bowls. The aroma of vanilla is strong.",
            "exits": {"west": "Entrance", "north": "Stale Dungeon"},
            "items": ["Fudge Key"]
        },
        "Stale Dungeon": {
            "desc": "Stale crumbs crunch underfoot. Only a path East remains.",
            "exits": {"east": "Vault", "south": "Kitchen"},
            "items": []
        },
        "Vault": {
            "desc": "A treasure vault glittering with cookie artifacts.",
            "exits": {"south": "Hallway", "east": "Secret Chamber"},
            "items": ["Silver Spoon"]
        },
        "Secret Chamber": {
            "desc": "You see the legendary Golden Whisk floating on a pedestal!",
            "exits": {"west": "Vault"},
            "items": ["Golden Whisk"]
        }
    }
    inventory = []
    current = "Entrance"

    def show_status():
        print(f"\nLocation: {C_DIM}{current}{C_RST}")
        print(rooms[current]["desc"])
        if rooms[current]["items"]:
            print("You see here: " + ", ".join(rooms[current]["items"]))
        print("Exits: " + ", ".join(rooms[current]["exits"].keys()))

    while True:
        show_status()
        cmd = input("\n> ").strip().lower().split()
        if not cmd:
            continue
        action = cmd[0]

        if action in ("go", "move"):
            if len(cmd) < 2:
                print("Go where?")
                continue
            direction = cmd[1]
            if direction not in rooms[current]["exits"]:
                print("You can't go that way.")
                continue
            # locked?
            if "locked" in rooms[current] and direction in rooms[current]["locked"]:
                req = rooms[current]["locked"][direction]
                if req not in inventory:
                    print(f"The door is locked. You need {req}.")
                    continue
            current = rooms[current]["exits"][direction]
        elif action in ("look",):
            continue  # will re-show status
        elif action in ("take", "get"):
            if len(cmd) < 2:
                print("Take what?")
                continue
            item = " ".join(cmd[1:])
            if item.title() in rooms[current]["items"]:
                rooms[current]["items"].remove(item.title())
                inventory.append(item.title())
                print(f"You picked up {item.title()}.")
            else:
                print("No such item here.")
        elif action in ("use",):
            if len(cmd) < 2:
                print("Use what?")
                continue
            item = " ".join(cmd[1:])
            if item.title() in inventory:
                if item.lower() == "golden whisk":
                    print(f"{C_OK}You wield the Golden Whisk! Baking perfection is yours!{C_RST}")
                    print("🎉 You won the adventure! 🎉")
                    break
                else:
                    print(f"You try using {item.title()}, but nothing happens.")
            else:
                print("You don't have that.")
        elif action in ("inventory", "i"):
            print("Inventory: " + (", ".join(inventory) if inventory else "(empty)"))
        elif action in ("quit", "exit"):
            print("Exiting Adventure.")
            break
        else:
            print("Unknown command. Commands: go/take/use/look/inventory/quit")

    input("\nPress Enter to return to menu...")

# --------------------
# 7. Emulator Warning
# --------------------
def emulator_warning():
    print(f"\n{C_ERR}[!!] Unauthorized copy of CookieOS detected!{C_RST}")
    print("System locking due to crumb-piracy 🛑")
    print("Insert Original CookieDisc™ to restore access.")
    input("\nPress Enter to return to menu...")

# --------------------
# 8. Shutdown
# --------------------
def shutdown():
    print(f"\n{C_OK}🍪 Shutting down CookieOS Deluxe…{C_RST}")
    sys.exit(f"{C_DIM}Goodbye! All crumbs safely saved.{C_RST}")

# --------------------
# Main Launcher
# --------------------
def main():
    MENU = {
        "1": ("File Explorer", file_explorer),
        "2": ("CookieNet Explorer", cookienet),
        "3": ("Control Panel", control_panel),
        "4": ("Multiplayer Minesweeper", minesweeper),
        "5": ("Reversi", reversi),
        "6": ("Adventure Mode", adventure),
        "7": ("Emulator Warning", emulator_warning),
        "8": ("Shutdown", shutdown),
    }
    # Auto-bake at startup?
    if settings["Auto-Bake at Startup"]:
        print(f"{C_OK}Auto-bake starting… fresh batch of cookies ready!{C_RST}")

    while True:
        print(f"\n{C_DIM}{'='*40}{C_RST}")
        print(f"{C_OK}     Welcome to CookieOS Deluxe 🍪     {C_RST}")
        print(f"{C_DIM}{'='*40}{C_RST}")
        for key, (name, _) in MENU.items():
            print(f"  {key}. {name}")
        choice = input("\nChoose an option (1–8): ").strip()
        if choice in MENU:
            MENU[choice][1]()
        else:
            print(f"{C_ERR}Invalid option.{C_RST}")

if __name__ == "__main__":
    main()
