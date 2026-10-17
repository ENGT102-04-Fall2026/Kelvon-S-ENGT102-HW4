# Kelvon-S-ENGT102-HW4
Battery Runtime Simulator:
import matplotlib.pyplot  as plt

Battery_Capacity_WH = 36.0

POWER_DICT = {
    "standby": 3.0,
    "sensing": 8.0,
    "drive": 30.0,
    "transmit": 18.0
    }

def calculate_energy(power_w, duration_min):
    duration_hr = duration_min / 60.0
    return power_w * duration_hr

def get_battery_status(remaining_wh, total_capacity_wh):
    if remaining_wh <= 0:
        return "BATTERY DEPLETED"
    elif (remaining_wh / total_capacity_wh) <= 0.20:
        return "LOW BATTERY"
    else:
        return "NORMAL"
def main():
    elapsed_times = [0.0]
    battery_levels = [Battery_Capacity_WH]
    
    current_time = 0.0
    current_battery = Battery_Capacity_WH 
    total_energy_required = 0.0

    print("--- Operating Schedule Simulation ---")
    print(f"{'Mode':<10} | {'Duration (min)':<14} | {'Energy (Wh)':<12} | {'Battery Remaining (Wh)':<22} | {'Status'}")
    print("-" * 75)
    
    with open("battery_schedule.csv", "r") as file:
        header = file.readline() 
        
        for line in file:
            line = line.strip()
            if not line:
                continue
            
            mode, duration_str = line.split(",")
            duration = float(duration_str)
            
            power = POWER_DICT[mode]
            energy_used = calculate_energy(power, duration)
            
            current_time += duration
            current_battery -= energy_used
            total_energy_required += energy_used
            
            elapsed_times.append(current_time)
            battery_levels.append(current_battery)
            
            status = get_battery_status(current_battery, Battery_Capacity_WH)
            print(f"{mode:<10} | {duration:<14.1f} | {energy_used:<12.2f} | {max(0, current_battery):<22.2f} | {status}")

    print("-" * 75)
    
    percentage_remaining = (current_battery /Battery_Capacity_WH) * 100.0
    can_complete = current_battery >= 0
    
    print("\n--- Final Simulation Summary ---")
    print(f"Total Operating Time: {current_time:.1f} minutes")
    print(f"Total Energy Required: {total_energy_required:.2f} Wh")
    print(f"Battery Energy Remaining: {current_battery:.2f} Wh")
    print(f"Percentage of Battery Remaining: {percentage_remaining:.2f}%")
    if can_complete:
        print("Schedule Result: SUCCESS - The battery can complete the full schedule.")
    else:
        print("Schedule Result: FAILED - The battery CANNOT complete the full schedule.")   
    
    plt.figure(figsize=(8, 5))
    plt.plot(elapsed_times, battery_levels, marker='o', linestyle='-', color='b', label='Battery Remaining (Wh)')
    plt.title('Battery Energy Remaining Over Operating Schedule')
    plt.xlabel('Elapsed Time (minutes)')
    plt.ylabel('Battery Energy Remaining (Wh)')
    plt.grid(True)
    plt.show()
    plt.tight_layout()
    plt.savefig('battery_runtime.png')
    print("\nPlot saved successfully as 'battery_runtime.png'.")

if __name__ == "__main__":
    main()
