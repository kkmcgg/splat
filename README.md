# splat

![image](https://github.com/kkmcgg/splat/assets/36888812/fa4b60c5-3613-4c52-ad93-7293a963e728)

**Consensus Blending** is a novel approach to enhance LiDAR data by capitalizing on overlapping pulse regions. By focusing on areas where pulses intersect, this method aims to boost the signal-to-noise ratio, ensuring that only consistent and corroborated data is emphasized. By treating overlapping LiDAR pulses as a form of consensus, or a "seconded motion", weak or inconsistent signals can be minimized or excluded, yielding a more refined and reliable point cloud. This technique has potential implications for improving point cloud quality in applications ranging from topographic mapping to autonomous vehicle navigation.

# Gaussian Splatting and Data Integration: A Deep Dive

Exploring the nature of observations, Gaussian splatting stands out as a versatile technique in computer graphics and data visualization. Our discussion ventured into the intricacies of integrating 4D observations and the role of Gaussian splatting in this context.

## The Universality of Gaussian Representations in Measurements

The Gaussian distribution, also known as the normal distribution, has been at the forefront of numerous scientific fields for centuries. Its ubiquitous nature in natural phenomena and its mathematical tractability make it a cornerstone for many theoretical and applied disciplines. When we turn our attention to measurements – be it from lidar, radar, cameras, or any other observational instrument – a compelling argument can be made for the Gaussian nature of these observations. Here's a breakdown of this logic:

### 1. **Central Limit Theorem (CLT)**:
The Central Limit Theorem, one of the foundational pillars of probability theory, states that the sum of a large number of independent and identically distributed random variables tends towards a Gaussian distribution, regardless of the original distribution of the variables. In the context of measurements, many observational errors (instrumental noise, environmental factors, etc.) can be considered as these random variables. When they accumulate, their combined effect converges towards a Gaussian distribution.

### 2. **Error Propagation**:
In measurement systems, errors propagate. When combining measurements or integrating data from multiple sources, these errors compound. Gaussian distributions, owing to their mathematical properties, allow for straightforward error propagation, making them a natural choice for representing combined measurement uncertainties.

### 3. **Bayesian Framework**:
Bayesian statistics often employ Gaussian distributions as priors due to their conjugate properties. Given that measurements can be seen as a process of updating our knowledge based on new data (a Bayesian perspective), representing observational uncertainties as Gaussians becomes a rational choice.

### 4. **Differential Perspective**:
When considering observations at a differential limit, any signal can be seen as symmetric in a multi-dimensional space, if it's differentiable. This symmetry hints at a Gaussian nature, especially when considering the infinitesimal neighborhood around a point in this space.

### 5. **Compensation Mechanism**:
For observations that might inherently not be Gaussian, one can argue that any non-symmetry or deviation from the Gaussian shape can be accounted for by the interaction of positive and negative Gaussians nearby in the observational field. This provides a mechanism to represent even non-Gaussian data in a Gaussian framework.

### 6. **Computational Efficacy**:
Gaussians, due to their mathematical form, are computationally efficient to work with. This makes them preferable in measurement systems where real-time processing or large-scale data integrations are required.

In summary, while not all measurements in their raw form are strictly Gaussian, the combined effect of various factors, both theoretical and practical, makes the Gaussian representation a robust and effective choice for a wide range of applications.


## The Gaussian Nature of Observations

All observations, from sources such as lidar, radar, cameras, or sound, can potentially be viewed as Gaussian. This perspective arises from the interpretation of observations as fields in a 4D space – comprising three spatial dimensions and time. Mathematically, this can be represented as:

$$\[ \text{Observation} = f(x, y, z, t) \]$$

The Bayesian approach emphasizes that all measurements should ideally come with a prior, possibly represented by a 4D Total Propagated Uncertainty (4DTPU). This leads us to an essential revelation:

$$\[ \text{Observation Complete} = \text{Observation} + 4DTPU \]$$

## Gaussian Splatting: The Heart of Integration

The essence of Gaussian splatting lies in its ability to approximate diverse behaviors through superposition. By judiciously placing and scaling Gaussian functions, even introducing negative amplitudes, asymmetric features in data can be effectively captured. In a 2D space, the Gaussian function is given by:

$$\[ G(x, y) = e^{-\frac{x^2 + y^2}{2\sigma^2}} \]$$

## Efficient Alternatives to Gaussian

While Gaussians are powerful, computational efficiency often dictates the search for alternatives:

1. **Epanechnikov Kernel**: Defined as \( K(u) = \frac{3(1 - u^2)}{4} \) for \( |u| \leq 1 \) and 0 otherwise.
2. **B-spline Kernels**: B-splines are piecewise polynomial functions, offering a balance between smoothness and computational efficiency.
3. **Piecewise Linear Kernel**: Represented as a triangle function, it's less smooth but computationally efficient.
4. **Wavelet Transform**: Wavelets like the Haar wavelet can provide localized frequency information.
5. **Look-up Tables (LUTs)**: Precomputed values of the Gaussian function, stored for faster access.
6. **Binomial Approximation**: A multi-pass algorithm that approaches a Gaussian shape.

## Introducing Negative Splatting

Traditional Gaussian splatting primarily utilizes positive kernel values. However, the concept of "negative splatting" brings forth a novel approach. By employing both positive and negative Gaussians, datasets with overlapping or contradictory information can be seamlessly integrated. This idea can be represented as:

$$\[ \text{Integrated Data} = \text{Positive Gaussian} - \text{Negative Gaussian} \]$$

## Key Terms and Concepts

1. **4DTPU (4D Total Propagated Uncertainty)**: Represents the uncertainty associated with an observation in a 4D space.
2. **NeRF (Neural Radiance Fields)**: A technique for 3D scene rendering from 2D images using deep learning.
3. **Differential Rendering**: Highlights the difference between two rendering techniques.
4. **Deconvolution**: An image processing technique that can sometimes yield negative values.
5. **Wavelet Transforms**: Decomposes signals into various frequency bands.

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

## Gaussian Alternatives (1D)

![image](https://github.com/kkmcgg/splat/assets/36888812/44ac00a4-4280-4023-9077-4cfd936a262e)


1. Epanechnikov Kernel (1D)

```float epanechnikov(float x) {
    if(abs(x) <= 1.0) {
        return 3.0 * (1.0 - x*x) / 4.0;
    }
    return 0.0;
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    uv = uv * 2.0 - 1.0; // Normalize to [-1,1]
    
    float value = epanechnikov(uv.x);
    
    fragColor = vec4(vec3(value), 1.0);
}
```

2. B-spline Kernel (1D)

```
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    uv = uv * 2.0 - 1.0; // Normalize to [-1,1]
    
    float value = bspline(uv.x);
    
    fragColor = vec4(vec3(value), 1.0);
}
```

3. Piecewise Linear Kernel

```
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    uv = uv * 2.0 - 1.0; // Normalize to [-1,1]
    
    float value = triangle(uv.x);
    
    fragColor = vec4(vec3(value), 1.0);
}
```

4. Haar Wavelet (as a simple representation for Wavelets)

```
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy;
    uv = uv * 2.0 - 1.0; // Normalize to [-1,1]
    
    float value = haarWavelet(uv.x);
    
    fragColor = vec4(vec3(value), 1.0);
}
```

## Gaussian Alternatives (2D)

![image](https://github.com/kkmcgg/splat/assets/36888812/d04c0daf-6d77-4781-9528-f63f8316a73f)


1. Epanechnikov Kernel (2D)
```
float epanechnikov(vec2 v) {
    float normSquared = dot(v, v);
    if(normSquared <= 1.0) {
        return 3.0/3.14 * (1.0 - normSquared);
    }
    return 0.0;
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy * 2.0 - 1.0; // Normalize to [-1,1]
    
    float value = epanechnikov(uv);
    
    fragColor = vec4(vec3(value), 1.0);
}
```
2. B-spline Kernel (2D)

```
float bspline(float t) {
    // ... same as before
}

float bspline2D(vec2 v) {
    return bspline(v.x) * bspline(v.y);
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy * 2.0 - 1.0;
    
    float value = bspline2D(uv);
    
    fragColor = vec4(vec3(value), 1.0);
}
```

3. Piecewise Linear Kernel (2D)

```
float triangle(float t) {
    // ... same as before
}

float triangle2D(vec2 v) {
    return triangle(v.x) * triangle(v.y);
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy * 2.0 - 1.0;
    
    float value = triangle2D(uv);
    
    fragColor = vec4(vec3(value), 1.0);
}
```

4. Haar Wavelet (2D)

```
float haarWavelet(float t) {
    // ... same as before
}

float haarWavelet2D(vec2 v) {
    return haarWavelet(v.x) * haarWavelet(v.y);
}

void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord/iResolution.xy * 2.0 - 1.0;
    
    float value = haarWavelet2D(uv);
    
    fragColor = vec4(vec3(value), 1.0);
}
```

# Chats
https://chat.openai.com/share/2a3c072a-ebb3-409b-bbb1-df9bb1eca75e
