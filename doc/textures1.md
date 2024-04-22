# Add Texture-mapping

22.4.2024

### Game development, SDL2. Ray casting corridors. How to add texture mapping to this code, low level rendering pixel techniques?

### LLAMA3-70B-INSTRUCT

To add texture mapping to your ray casting code, you'll need to make the following changes:

Load textures: Load your textures into memory using a library like SDL_image or stb_image. Store the texture data in a struct, along with its width, height, and pixel format.
Associate textures with walls: Modify your wall struct to include a pointer to a texture struct. This will allow you to associate a texture with each wall.
Calculate texture coordinates: In your render function, calculate the texture coordinates (u, v) for each wall segment. You can do this by projecting the wall's 3D coordinates onto the 2D texture space.
Sample texture colors: Use the calculated texture coordinates to sample the texture color at the corresponding pixel. You can use bilinear filtering to interpolate between neighboring texture pixels.
Blend texture colors with wall shade: Combine the sampled texture color with the wall shade (calculated based on the light direction and wall normal) to produce the final pixel color.
Update verline function: Modify the verline function to use the textured pixel color instead of the solid color.
Here's some sample code to get you started:

```
// Texture struct
typedef struct {
    u32 *pixels; // texture pixel data
    int width, height; // texture dimensions
    int pitch; // texture pitch ( bytes per row )
} texture_t;

// Associate texture with wall
typedef struct wall {
    v2i a, b;
    int portal;
    texture_t *texture; // pointer to associated texture
} wall_t;

// Calculate texture coordinates
void calc_tex_coords(wall_t *wall, v2 p, f32 *u, f32 *v) {
    // Calculate texture coordinates based on wall's 3D coordinates
    // and the player's camera position and angle
    // ...
}

// Sample texture color
u32 sample_texture(texture_t *texture, f32 u, f32 v) {
    // Bilinear filtering
    int x = (int)u;
    int y = (int)v;
    f32 dx = u - x;
    f32 dy = v - y;

    u32 c00 = texture->pixels[y * texture->pitch + x * 4];
    u32 c10 = texture->pixels[y * texture->pitch + (x + 1) * 4];
    u32 c01 = texture->pixels[(y + 1) * texture->pitch + x * 4];
    u32 c11 = texture->pixels[(y + 1) * texture->pitch + (x + 1) * 4];

    u32 r = (u32)((c00 >> 16) + dx * ((c10 >> 16) - (c00 >> 16)));
    u32 g = (u32)((c00 >> 8) + dx * ((c10 >> 8) - (c00 >> 8)));
    u32 b = (u32)((c00) + dx * ((c10) - (c00)));

    r += dy * ((u32)((c01 >> 16) + dx * ((c11 >> 16) - (c01 >> 16))) - r);
    g += dy * ((u32)((c01 >> 8) + dx * ((c11 >> 8) - (c01 >> 8))) - g);
    b += dy * ((u32)((c01) + dx * ((c11) - (c01))) - b);

    return (r << 16) | (g << 8) | b;
}

// Update verline function
void verline(int x, int y0, int y1, wall_t *wall) {
    f32 u, v;
    calc_tex_coords(wall, (v2){x, 0}, &u, &v);
    u32 tex_color = sample_texture(wall->texture, u, v);

    // Blend texture color with wall shade
    u32 shade = 255 - (wall->portal ? 128 : 0);
    u32 color = (tex_color & 0x00FFFFFF) | (shade << 24);

    for (int y = y0; y <= y1; y++) {
        state.pixels[y * SCREEN_WIDTH + x] = color;
    }
}

```

Note that this is just a starting point, and you'll need to adjust the code to fit your specific requirements. You may also want to consider using a more advanced texture mapping technique, such as affine texture mapping or perspective-correct texture mapping.
