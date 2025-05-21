Tx lottery pick 3 generator 


import random
from collections import defaultdict
import datetime

# Mock data structure for past 30 days of TX Pick 3 drawings
# In a real implementation, you would pull this from official lottery results
def get_mock_past_drawings():
    drawings = {
        'morning': [],
        'day': [],
        'evening': [],
        'night': []
    }
    
    # Generate mock historical data (normally you'd use real results)
    for i in range(30):
        date = datetime.date.today() - datetime.timedelta(days=30-i)
        drawings['morning'].append((date, ''.join([str(random.randint(0,9)) for _ in range(3)])))
        drawings['day'].append((date, ''.join([str(random.randint(0,9)) for _ in range(3)])))
        drawings['evening'].append((date, ''.join([str(random.randint(0,9)) for _ in range(3)])))
        drawings['night'].append((date, ''.join([str(random.randint(0,9)) for _ in range(3)])))
    
    return drawings

def analyze_drawings(drawings):
    analysis = {
        'frequency': defaultdict(int),
        'time_patterns': defaultdict(int),
        'digit_positions': [defaultdict(int) for _ in range(3)],
        'hot_numbers': [],
        'cold_numbers': [],
        'common_pairs': defaultdict(int)
    }
    
    # Analyze all drawings
    for time_of_day, numbers_list in drawings.items():
        for date, number in numbers_list:
            # Frequency analysis
            analysis['frequency'][number] += 1
            
            # Time of day patterns
            analysis['time_patterns'][f"{time_of_day}_{number}"] += 1
            
            # Digit position analysis
            for pos in range(3):
                analysis['digit_positions'][pos][number[pos]] += 1
            
            # Pair analysis
            analysis['common_pairs'][number[:2]] += 1
            analysis['common_pairs'][number[1:]] += 1
    
    # Determine hot and cold numbers
    sorted_freq = sorted(analysis['frequency'].items(), key=lambda x: x[1], reverse=True)
    analysis['hot_numbers'] = [num for num, count in sorted_freq[:5]]
    analysis['cold_numbers'] = [num for num, count in sorted_freq[-5:]]
    
    return analysis

def generate_suggestions(analysis):
    suggestions = {
        'evening': [],
        'day': [],
        'night': []
    }
    
    # Strategy 1: Hot numbers
    suggestions['evening'].extend(analysis['hot_numbers'][:3])
    suggestions['day'].extend(analysis['hot_numbers'][1:4])
    suggestions['night'].extend(analysis['hot_numbers'][2:5])
    
    # Strategy 2: Position analysis
    for time in suggestions:
        pos_based = []
        for pos in range(3):
            most_common = sorted(analysis['digit_positions'][pos].items(), key=lambda x: x[1], reverse=True)
            pos_based.append(str(most_common[0][0]))
        suggestions[time].append(''.join(pos_based))
    
    # Strategy 3: Time-specific patterns
    for time in suggestions:
        time_patterns = [k.split('_')[1] for k,v in analysis['time_patterns'].items() if k.startswith(time)]
        if time_patterns:
            suggestions[time].append(max(set(time_patterns), key=time_patterns.count))
    
    return suggestions

# Main function
def tx_pick3_generator():
    print("Texas Pick 3 Lottery Number Generator")
    print("Analyzing past 30 days of drawings...\n")
    
    # Get past drawings (in real use, replace with actual data)
    past_drawings = get_mock_past_drawings()
    
    # Analyze patterns
    analysis = analyze_drawings(past_drawings)
    
    # Generate suggestions
    suggestions = generate_suggestions(analysis)
    
    # Display results
    print("Analysis Results:")
    print(f"Most frequent numbers: {', '.join(analysis['hot_numbers'])}")
    print(f"Least frequent numbers: {', '.join(analysis['cold_numbers'])}\n")
    
    print("Suggested Numbers:")
    for time, numbers in suggestions.items():
        print(f"{time.capitalize()} drawing: {', '.join(numbers[:5])}")
    
    print("\nRemember: Lottery numbers are random. Play responsibly.")

# Run the generator
tx_pick3_generator()