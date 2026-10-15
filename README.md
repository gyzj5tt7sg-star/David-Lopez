import matplotlib.pyplot as plt
#Libary for cars,location,and distances for locations
cars = [
    ["Hummer EV", 0.63],
    ["CyberTruck", 0.50],
    ["Challenger EV", 0.32],
    ["Tesla Roadster", 0.39],
    ["Lucid air", 0.23]
]
 
location = [
    ["UC Merced", 0],
    ["Lake yosemite", 2.5],
    ["Starbucks(Yosemite Ave/G St)", 4.5],
    ["In and out", 8.0],
    ["Merced Mall", 8.0],
    ["Sahara cafe", 8.5],
    ["Smash Bros Burgers", 8.5]
]

distances = [
    [0.0, 2.5, 4.5, 8.0, 8.0, 8.5, 8.5],   # UC Merced
    [2.5, 0.0, 5.5, 9.5, 9.5, 10.0, 10.0], # Lake Yosemite
    [4.5, 5.5, 0.0, 4.0, 5.0, 5.5, 5.5],   # Starbucks
    [8.0, 9.5, 4.0, 0.0, 2.0, 2.5, 2.5],   # Merced Mall
    [8.0, 9.5, 5.0, 2.0, 0.0, 1.8, 1.8],   # In-N-Out
    [8.5, 10.0, 5.5, 2.5, 1.8, 0.0, 0.2],  # Sahara Cafe
    [8.5, 10.0, 5.5, 2.5, 1.8, 0.2, 0.0]   # Smash Bros
]
#Indroduction/choosing a car
print("=" * 30)
print(" UC MERCED EV TRIP CALCULATOR")
print("=" * 30)

name = input("Whats your name?: ")
print(f"Hello {name}, you start at UC MERCED. What car would you like to use?")

for i in range(len(cars)):
    print(f"{i + 1}. {cars[i][0]} ({cars[i][1]} kWh/mi)")
car_chosen = int(input("Enter choice (1-5): ")) - 1

car_name = cars[car_chosen][0]
rate = cars[car_chosen][1]

current_location = 0
total_miles = 0.0
total_kwh = 0.0
#Menu to choose location,view history, and exit
while True:
    print(f"\nCurrent Spot: {location[current_location][0]}")
    print(f"Total Driven: {total_miles:.1f} miles | Energy Used: {total_kwh:.2f} kWh")
    print("1. Drive somewhere")
    print("2. View history file")
    print("3. Exit")

    choice = input("Choice (1-3): ").strip()
    #Option 1
    if choice == "1":
        print("\nWhere to next?")
        for i in range(len(location)):
            miles_away = distances[current_location][i]
            print(f"{i + 1}. {location[i][0]} ({miles_away} mi)")
            
        next_loc = int(input("Pick destination (1-7): ")) - 1
        
        if next_loc == current_location:
            print("You are already there!")
            continue
            
        leg_miles = distances[current_location][next_loc]
        leg_kwh = round(leg_miles * rate, 2)
        
        total_miles = round(total_miles + leg_miles, 2)
        total_kwh = round(total_kwh + leg_kwh, 2)
        
        from_place = location[current_location][0]
        to_place = location[next_loc][0]
        
        # Saving info to ev_miles.txt file
        file = open("ev_miles.txt", "a")
        file.write(f"{car_name},{from_place},{to_place},{leg_miles},{total_miles}\n")
        file.close()
        
        print(f"\nDrove {leg_miles} miles to {to_place}.")
        print(f"Leg energy: {leg_kwh} kWh.")
        print("Saved to ev_miles.txt!")
        
        current_location = next_loc
     #Optoin 2/Saved information.
    elif choice == "2":
        try:
            file = open("ev_miles.txt", "r")
            lines = file.readlines()
            file.close()
            print("\n--- Trip History ---")
            for line in lines:
                row = line.strip().split(",")
                if len(row) >= 5:
                    print(f"{row[0]}: {row[1]} -> {row[2]} ({row[3]} mi) | Odometer: {row[4]} mi")
        except FileNotFoundError:
            print("No history recorded yet.")
        #end calculator/graphed is uploaded
    elif choice == "3":
        print(f"\nFinal Total: {total_miles:.1f} miles driven and {total_kwh} used in {car_name}.")
        print("Generating graphs...")
        print("Goodbye",name)
        break

#graphing stuff
miles = [total_miles]
kwh = [total_kwh]

# 1. Creates the line
plt.plot(miles, kwh, marker="o", color="blue", linewidth=2, label=car_name)

# 2. Add labels, title, and a grid
plt.title("Energy Used vs. Distance", fontsize=14)
plt.xlabel("Distance (miles)")
plt.ylabel("Energy Used (kWh)")
plt.grid(True, linestyle="--", alpha=0.6)
plt.legend()

# 3. Show or save
plt.tight_layout()
plt.savefig("my_line_chart.png")  # Saves as an image
plt.show()
