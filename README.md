# Gouraud-Shading-and-Phong-Shading #

In this project I implemented Gouraud Shading and Phong Shading on Phong Reflection Model.
## Introduction: ##
Before talking about Gouraud Shading and Phong Shading,we need to know the reflection model first.The "standard" reflection model in computer graphics that compromises between acceptable results and processing cost is the Phong model. The Phong model describes the interaction of light with a surface, in terms of the properties of the surface and the nature of the incident light. The reflection model is the basic factor in the look of a three dimensional shaded object. It enables a two dimensional screen projection of an object to look real. The Phong model reflected light in terms of a diffuse and specular component together with an ambient term. The intensity of a point on a surface is taken to be the linear combination of these three components. 

**(1) Diffuse Reflection:**

![](https://github.com/ruange/Gouraud-Shading-and-Phong-Shading/blob/master/photo%20and%20gif/diffuse.JPG)

**(2) Ambient Light:**

![](https://github.com/ruange/Gouraud-Shading-and-Phong-Shading/blob/master/photo%20and%20gif/ambient.JPG)

**(3)Specular Reflection:**

![](https://github.com/ruange/Gouraud-Shading-and-Phong-Shading/blob/master/photo%20and%20gif/specular.JPG)

**Put all together:**

![](https://github.com/ruange/Gouraud-Shading-and-Phong-Shading/blob/master/photo%20and%20gif/together.JPG)

After knowing the Phong model,we now talk about the difference between Gouraud Shading and Phong Shading.The Gouraud Shading method applies the phong model
on a subset of surface points and interpolates the intensity of
the remaining points on the surface. In the case of a
polygonal mesh the illumination model is usually applied at
each vertex and the colors in the triangles interior are linearly
interpolated from these vertex values during polygon
rasterization.

Unlike Gouraud shading, which interpolates colors across polygons, in Phong shading a normal vector is linearly interpolated across the surface of the polygon from the polygon's vertex normals. The surface normal is interpolated and normalized at each pixel and then used in a reflection model, e.g. the Phong reflection model, to obtain the final pixel color. Phong shading is more computationally expensive than Gouraud shading since the reflection model must be computed at each pixel instead of at each vertex.

## Implement details: ##

Gouraud shading (AKA Smooth Shading) is a per-vertex color computation. What this means is that the vertex shader must determine a color for each vertex and pass the color as an out variable to the fragment shader. Since this color is passed to the fragment shader as an in varying variable, it is interpolated across the fragments thus giving the smooth shading.Here is an illustration of the Gouraud Shading:

![](https://github.com/ruange/Gouraud-Shading-and-Phong-Shading/blob/master/photo%20and%20gif/Gouraudshading%20process.jpg)

The Gouraud Shading vertex shader:

    out vec3 vertex_color;
	void main(){
	vec3 v = vec3(gl_ModelViewMatrix * gl_Vertex);
   	vec3 N = normalize(gl_NormalMatrix * gl_Normal);
   
   	vec3 L = normalize(gl_LightSource[0].position.xyz - v);   
   	vec3 E = normalize(-v);       // we are in Eye Coordinates, so EyePos is (0,0,0)  
   	vec3 R = normalize(-reflect(L,N));  
 
   	//calculate Ambient Term:  
   	vec4 Iamb = gl_FrontLightProduct[0].ambient;    

   	//calculate Diffuse Term:  
   	vec4 Idiff = gl_FrontLightProduct[0].diffuse * max(dot(N,L), 0.0);    
   
   	// calculate Specular Term:
   	vec4 Ispec = gl_FrontLightProduct[0].specular 
                * pow(max(dot(R,E),0.0),0.3*gl_FrontMaterial.shininess);
 
   	vertex_color = gl_FrontLightModelProduct.sceneColor + Iamb + Idiff + Ispec; 
   
   	gl_Position = gl_ModelViewProjectionMatrix * gl_Vertex;
	}
The fragment shader:
    
	in vec3 vertex_color;   

	void main (void)  
	{    
  	 gl_FragColor = vertex_color;   
	}

In contrast, Phong shading is a per-fragment color computation. The vertex shader provides the normal and position data as out variables to the fragment shader. The fragment shader then interpolates these variables and computes the color.
Here is an illustration of the Phong Shading:

![](https://github.com/ruange/Gouraud-Shading-and-Phong-Shading/blob/master/photo%20and%20gif/Phongshading%20process.jpg)

The Phong Shading vertex shader:

	varying vec3 N;
	varying vec3 v;

	void main(void)
	{

   	v = vec3(gl_ModelViewMatrix * gl_Vertex);       
  	N = normalize(gl_NormalMatrix * gl_Normal);

   	gl_Position = gl_ModelViewProjectionMatrix * gl_Vertex;
	}

The Phong Shading fragment shader:

	varying vec3 N;
	varying vec3 v;    

	void main (void)  
	{  
   	vec3 L = normalize(gl_LightSource[0].position.xyz - v);   
   	vec3 E = normalize(-v);       // we are in Eye Coordinates, so EyePos is (0,0,0)  
   	vec3 R = normalize(-reflect(L,N));  
 
   	//calculate Ambient Term:  
   	vec4 Iamb = gl_FrontLightProduct[0].ambient;    

   	//calculate Diffuse Term:  
   	vec4 Idiff = gl_FrontLightProduct[0].diffuse * max(dot(N,L), 0.0);    
   
   	// calculate Specular Term:
   	vec4 Ispec = gl_FrontLightProduct[0].specular 
                * pow(max(dot(R,E),0.0),0.3*gl_FrontMaterial.shininess);

   	// write Total Color:  
   	gl_FragColor = gl_FrontLightModelProduct.sceneColor + Iamb + Idiff + Ispec;   
	}

##  display: ##

Gouraud Shading:

![](https://github.com/ruange/Gouraud-Shading-and-Phong-Shading/blob/master/photo%20and%20gif/GouraudShading.gif)

Phong Shading:

![](https://github.com/ruange/Gouraud-Shading-and-Phong-Shading/blob/master/photo%20and%20gif/PhongShading.gif)

