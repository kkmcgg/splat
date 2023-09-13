# splat

![image](https://github.com/kkmcgg/splat/assets/36888812/fa4b60c5-3613-4c52-ad93-7293a963e728)

**Consensus Blending** is a novel approach to enhance LiDAR data by capitalizing on overlapping pulse regions. By focusing on areas where pulses intersect, this method aims to boost the signal-to-noise ratio, ensuring that only consistent and corroborated data is emphasized. By treating overlapping LiDAR pulses as a form of consensus, or a "seconded motion", weak or inconsistent signals can be minimized or excluded, yielding a more refined and reliable point cloud. This technique has potential implications for improving point cloud quality in applications ranging from topographic mapping to autonomous vehicle navigation.


# Shadertoys

https://www.shadertoy.com/new

## Two Gaussians
![image](https://github.com/kkmcgg/splat/assets/36888812/86b4e984-f6f8-49c3-9d5a-90972c6cb857)

```
// Gaussian function
float gaussian( vec2 pos, vec2 center, float sigma ) {
    float distSq = length(pos - center) * length(pos - center);
    return exp(-distSq / (2.0 * sigma * sigma));
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    
    // Parameters for our Gaussians
    vec2 center1 = vec2(0.3, 0.5); // First Gaussian's center
    vec2 center2 = vec2(0.7, 0.5); // Second Gaussian's center
    float sigma = 0.1; // width of the Gaussians

    // Evaluate the two Gaussians at the current pixel
    float g1 = gaussian(uv, center1, sigma);
    float g2 = gaussian(uv, center2, sigma);

    // Combine the two Gaussians (just add them together for now)
    float combined = g1 + g2;

    // Output the result as a grayscale color
    fragColor = vec4(vec3(combined), 1.0);
}
```
## Two Gaussian Lens
![image](https://github.com/kkmcgg/splat/assets/36888812/33a43c2c-e29a-4068-8f33-9a62da750086)
```
// Gaussian function
float gaussian( vec2 pos, vec2 center, float sigma ) {
    float distSq = length(pos - center) * length(pos - center);
    return exp(-distSq / (2.0 * sigma * sigma));
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    
    // Parameters for our Gaussians
    vec2 center1 = vec2(0.3, 0.5); // First Gaussian's center
    vec2 center2 = vec2(0.7, 0.5); // Second Gaussian's center
    float sigma = 0.1; // width of the Gaussians
    //float threshold = 0.01; // Minimum value for the consensus
    float threshold = iChannel0.x;

    // Evaluate the two Gaussians at the current pixel
    float g1 = gaussian(uv, center1, sigma);
    float g2 = gaussian(uv, center2, sigma);

    // Determine where both Gaussians are above the threshold
    float combined;
    if (g1 > threshold && g2 > threshold) {
        combined = g1 + g2;
    } else {
        combined = 0.0;
    }

    // Output the result as a grayscale color
    fragColor = vec4(vec3(combined), 1.0);
}
```

## Interactive Lens
![image](https://github.com/kkmcgg/splat/assets/36888812/201a9653-7887-4333-9eb4-e2ea673a0faf)

```
// Gaussian function
float gaussian( vec2 pos, vec2 center, float sigma ) {
    float distSq = length(pos - center) * length(pos - center);
    return exp(-distSq / (2.0 * sigma * sigma));
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    
    float mouseNormalizedx = clamp(iMouse.x / iResolution.x, 0.0, 1.0); // Normalize mouse x to [0,1]
    float sigma = mix(0.01, 0.2, mouseNormalizedx); // Map to desired range for sigma
    
    float mouseNormalizedy = clamp(iMouse.y / iResolution.y, 0.0, 1.0); // Normalize mouse y to [0,1]
    float threshold = mix(0.01, 0.2, mouseNormalizedy); // Map to desired range for sigma

    
    // Parameters for our Gaussians
    vec2 center1 = vec2(0.3, 0.5); // First Gaussian's center
    vec2 center2 = vec2(0.7, 0.5); // Second Gaussian's center
    //float sigma = 0.1; // width of the Gaussians
    //float threshold = 0.01; // Minimum value for the consensus


    // Evaluate the two Gaussians at the current pixel
    float g1 = gaussian(uv, center1, sigma);
    float g2 = gaussian(uv, center2, sigma);

    // Determine where both Gaussians are above the threshold
    float combined;
    if (g1 > threshold && g2 > threshold) {
        combined = g1 + g2;
    } else {
        combined = 0.0;
    }

    // Output the result as a grayscale color
    fragColor = vec4(vec3(combined), 1.0);
}

```

## Two Gaussian Interactive
![image](https://github.com/kkmcgg/splat/assets/36888812/10607307-baad-444a-99d5-faa02a4bd6a6)

```
// Gaussian function
float gaussian( vec2 pos, vec2 center, float sigma ) {
    float distSq = length(pos - center) * length(pos - center);
    return exp(-distSq / (2.0 * sigma * sigma));
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    
    float mouseNormalizedx = clamp(iMouse.x / iResolution.x, 0.0, 1.0); // Normalize mouse x to [0,1]
    float sigma1 = mix(0.01, 0.2, mouseNormalizedx); // Map to desired range for sigma
    
    float mouseNormalizedy = clamp(iMouse.y / iResolution.y, 0.0, 1.0); // Normalize mouse y to [0,1]
    float sigma2 = mix(0.01, 0.2, mouseNormalizedy); // Map to desired range for sigma

    
    
    
    // Parameters for our Gaussians
    vec2 center1 = vec2(0.3, 0.5); // First Gaussian's center
    vec2 center2 = vec2(0.7, 0.5); // Second Gaussian's center
    float sigma = 0.1; // width of the Gaussians

    // Evaluate the two Gaussians at the current pixel
    float g1 = gaussian(uv, center1, sigma1);
    float g2 = gaussian(uv, center2, sigma2);

    // Combine the two Gaussians (just add them together for now)
    float combined = g1 + g2;

    // Output the result as a grayscale color
    fragColor = vec4(vec3(combined), 1.0);
}

```

## Lens with Interactive Gaussians

![image](https://github.com/kkmcgg/splat/assets/36888812/691aa0d9-74e5-45a6-a97d-2bba695e7e12)

```
// Gaussian function
float gaussian( vec2 pos, vec2 center, float sigma ) {
    float distSq = length(pos - center) * length(pos - center);
    return exp(-distSq / (2.0 * sigma * sigma));
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    
    float mouseNormalizedx = clamp(iMouse.x / iResolution.x, 0.0, 1.0); // Normalize mouse x to [0,1]
    float sigma1 = mix(0.01, 0.2, mouseNormalizedx); // Map to desired range for sigma
    
    float mouseNormalizedy = clamp(iMouse.y / iResolution.y, 0.0, 1.0); // Normalize mouse y to [0,1]
    float sigma2 = mix(0.01, 0.2, mouseNormalizedy); // Map to desired range for sigma

    float threshold = .01;
    
    
    
    // Parameters for our Gaussians
    vec2 center1 = vec2(0.3, 0.5); // First Gaussian's center
    vec2 center2 = vec2(0.7, 0.5); // Second Gaussian's center
    float sigma = 0.1; // width of the Gaussians

    // Evaluate the two Gaussians at the current pixel
    float g1 = gaussian(uv, center1, sigma1);
    float g2 = gaussian(uv, center2, sigma2);

    // Combine the two Gaussians (just add them together for now)
    float combined;
    if (g1 > threshold && g2 > threshold) {
        combined = g1 + g2;
    } else {
        combined = 0.0;
    }


    // Output the result as a grayscale color
    fragColor = vec4(vec3(combined), 1.0);
}

```

## Multiplicative Gaussians 
![image](https://github.com/kkmcgg/splat/assets/36888812/d5e4af4e-3957-4cf4-bf59-95b0836d82ac)

```
// Gaussian function
float gaussian( vec2 pos, vec2 center, float sigma ) {
    float distSq = length(pos - center) * length(pos - center);
    return exp(-distSq / (2.0 * sigma * sigma));
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    
    float mouseNormalizedx = clamp(iMouse.x / iResolution.x, 0.0, 1.0); // Normalize mouse x to [0,1]
    float sigma1 = mix(0.01, 0.2, mouseNormalizedx); // Map to desired range for sigma
    
    float mouseNormalizedy = clamp(iMouse.y / iResolution.y, 0.0, 1.0); // Normalize mouse y to [0,1]
    float sigma2 = mix(0.01, 0.2, mouseNormalizedy); // Map to desired range for sigma

    
    
    
    // Parameters for our Gaussians
    vec2 center1 = vec2(0.3, 0.5); // First Gaussian's center
    vec2 center2 = vec2(0.7, 0.5); // Second Gaussian's center
    float sigma = 0.1; // width of the Gaussians

    // Evaluate the two Gaussians at the current pixel
    float g1 = gaussian(uv, center1, sigma1);
    float g2 = gaussian(uv, center2, sigma2);

    // Combine the two Gaussians (just add them together for now)
    float combined = g1 * g2;

    // Output the result as a grayscale color
    fragColor = vec4(vec3(combined), 1.0);
}

```
## Battling Gaussians 
![image](https://github.com/kkmcgg/splat/assets/36888812/abfa74e7-8058-4cca-9fac-8e4d38dbe6c7)

```
//float size = 2.0;
//float contrast = 1.0;

float size = 10.0;
float contrast = 10.0;

float background = 0.5;

// Gaussian function
float gaussian( vec2 pos, vec2 center, float sigma ) {
    float distSq = length(pos - center) * length(pos - center);
    return exp(-distSq / (size * sigma * sigma));
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    
    float mouseNormalizedx = clamp(iMouse.x / iResolution.x, 0.0, 1.0); // Normalize mouse x to [0,1]
    float magnitude1 = mix(-contrast, contrast, mouseNormalizedx); // Map mouse x to [-1, 1] for magnitude
    
    float mouseNormalizedy = clamp(iMouse.y / iResolution.y, 0.0, 1.0); // Normalize mouse y to [0,1]
    float magnitude2 = mix(-contrast, contrast, mouseNormalizedy); // Map mouse y to [-1, 1] for magnitude
    
    float sigma = 0.1; // Width of the Gaussians

    // Parameters for our Gaussians
    vec2 center1 = vec2(0.3, 0.5); // First Gaussian's center
    vec2 center2 = vec2(0.7, 0.5); // Second Gaussian's center

    // Evaluate the two Gaussians at the current pixel
    float g1 = magnitude1 * gaussian(uv, center1, sigma);
    float g2 = magnitude2 * gaussian(uv, center2, sigma);

    // Combine the two Gaussians
    float combined = background + (g1 + g2) * 0.5;

    // Ensure the result stays in the [0,1] range
    combined = clamp(combined, 0.0, 1.0);

    // Output the result as a grayscale color
    fragColor = vec4(vec3(combined), 1.0);
}
```

## Battling Lensed Gaussians 
![image](https://github.com/kkmcgg/splat/assets/36888812/1c4d7731-771d-4608-898a-fb5ff3cae431)
```
//float size = 2.0;
//float contrast = 1.0;

float size = 10.0;
float contrast = 10.0;

float background = 0.5;
float threshold = .9;

// Gaussian function
float gaussian( vec2 pos, vec2 center, float sigma ) {
    float distSq = length(pos - center) * length(pos - center);
    return exp(-distSq / (size * sigma * sigma));
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    
    float mouseNormalizedx = clamp(iMouse.x / iResolution.x, 0.0, 1.0); // Normalize mouse x to [0,1]
    float magnitude1 = mix(-contrast, contrast, mouseNormalizedx); // Map mouse x to [-1, 1] for magnitude
    
    float mouseNormalizedy = clamp(iMouse.y / iResolution.y, 0.0, 1.0); // Normalize mouse y to [0,1]
    float magnitude2 = mix(-contrast, contrast, mouseNormalizedy); // Map mouse y to [-1, 1] for magnitude
    
    float sigma = 0.1; // Width of the Gaussians

    // Parameters for our Gaussians
    vec2 center1 = vec2(0.3, 0.5); // First Gaussian's center
    vec2 center2 = vec2(0.7, 0.5); // Second Gaussian's center

    // Evaluate the two Gaussians at the current pixel
    float g1 = magnitude1 * gaussian(uv, center1, sigma);
    float g2 = magnitude2 * gaussian(uv, center2, sigma);

    // Combine the two Gaussians
    float combined = background + (g1 + g2) * 0.5;

    // Ensure the result stays in the [0,1] range
    combined = clamp(combined, 0.0, 1.0);
    
    if (combined > threshold) {
        combined = combined;
    } else {
        combined = 0.0;
    }


    // Output the result as a grayscale color
    fragColor = vec4(vec3(combined), 1.0);
}
```
