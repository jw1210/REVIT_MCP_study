---
description: How to perform a corridor building code review (width check) in Revit
---

# Corridor Code Review Workflow

This workflow describes the logic for checking corridor width compliance and creating annotation dimensions in Revit using the MCP tools.

## 1. Preparation
Ensure Revit is open and the correct floor plan view is active.
- Use `get_active_view` to get the current view ID.

## 2. Identify Corridor
- If a specific room is known, use `get_room_info` to get its BoundingBox and Center point.
- If not, use `select_element` to ask the user to select the corridor, or `query_elements` filter by 'Room'.

## 3. Get Wall Data (Crucial Step)
Do **not** rely solely on Room BoundingBox for exact dimensions as it may not reflect wall finishes or structure correctly.
- Use `query_walls_by_location` with the corridor's approximate center `(x, y)`.
- Set `searchRadius` appropriately (e.g., 3000-5000mm).
- Filter the results to find the boundary walls (usually the two closest parallel walls).

## 4. Determine Dimension Points
From the wall data, identify two sets of coordinates:
1.  **Net Width (Effective Width)**: Use `Face1` or `Face2` of the walls (the faces facing the corridor). This is used for code compliance.
2.  **Structural Width (Centerline)**: Use the `LocationLine` of the walls.

## 5. Create Dual Dimensions
Create two separate dimension lines using `create_dimension` to provide complete context.
**Do not** make them overlap.

- **Dimension 1 (Code Compliance)**:
    - Coordinates: Wall Interior Faces.
    - Offset: Smaller value (e.g., 1200mm).
- **Dimension 2 (Reference)**:
    - Coordinates: Wall Centerlines.
    - Offset: Larger value (e.g., 2000mm).

## 6. Compliance Check
Compare the **Net Width** against regulations:
- **Japan (Building Standards Act)**:
    - Single-sided rooms: ≥ 1.2m
    - Double-sided rooms: ≥ 1.6m
- **Taiwan (Building Technical Regulations)**:
    - Single-sided rooms: ≥ 1.2m
    - Double-sided rooms: ≥ 1.6m

## Example Tool Chain
1. `get_active_view` -> ViewId
2. `query_walls_by_location` -> Wall Coordinates
3. `create_dimension` (Interior Face)
4. `create_dimension` (Centerline)
