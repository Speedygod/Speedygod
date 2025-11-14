import networkx as nx

# Sample campus graph: nodes are points, edges have weights (distances in feet)
# In a real app, load from a file or database
G = nx.Graph()
G.add_edge("Entrance", "Building A", weight=200)
G.add_edge("Building A", "Intersection 1", weight=150)
G.add_edge("Intersection 1", "Building B", weight=100)
G.add_edge("Intersection 1", "Cafeteria", weight=250)
G.add_edge("Cafeteria", "Library", weight=300)
G.add_edge("Library", "Building C", weight=180)
# Add more edges as needed

def get_route(start, end):
    if start not in G or end not in G:
        return "Invalid start or end point."
    
    try:
        # Find shortest path
        path = nx.shortest_path(G, source=start, target=end, weight='weight')
        distances = nx.shortest_path_length(G, source=start, target=end, weight='weight')
        
        # Generate instructions (simple: list turns based on path)
        instructions = []
        for i in range(len(path) - 1):
            current = path[i]
            next_node = path[i+1]
            instructions.append(f"Walk from {current} to {next_node}.")
        
        # Estimates
        walking_speed_fps = 4.4  # feet per second (3 mph)
        time_seconds = distances / walking_speed_fps
        time_minutes = time_seconds / 60
        steps = distances / 2  # 2 feet per step
        
        return {
            "path": path,
            "instructions": instructions,
            "distance_feet": distances,
            "estimated_time_minutes": round(time_minutes, 1),
            "estimated_steps": int(steps)
        }
    except nx.NetworkXNoPath:
        return "No route found."

# Example usage
if __name__ == "__main__":
    start = input("Enter starting point: ")
    end = input("Enter destination: ")
    result = get_route(start, end)
    if isinstance(result, dict):
        print(f"Route: {' -> '.join(result['path'])}")
        print("Instructions:")
        for instr in result['instructions']:
            print(f"- {instr}")
        print(f"Total distance: {result['distance_feet']} feet")
        print(f"Estimated walking time: {result['estimated_time_minutes']} minutes")
        print(f"Estimated steps: {result['estimated_steps']}")
    else:
        print(result)

