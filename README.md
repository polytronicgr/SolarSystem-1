# SolarSystem
A solar system simulator with velocity Verlet, using OpenGL for displaying.

A detailed description of the project will be found on the computational physics blog: http://compphys.go.ro/newtonian-gravity/

TOOLS

The project compiles on Windows with Visual Studio 2015.

LIBRARIES

For this project you'll need to download glm and glew. I installed them in C:\LIBs, if you use other paths you'll need to change the project settings accordingly.

OpenGL, 
mfc: included with Visual Studio, 
glm : http://glm.g-truc.net/0.9.7/index.html ,
glew: http://glew.sourceforge.net/

CLASSES

First, the classes generated by the MFC Application Wizard:

CAboutDlg - What the name says, I don't think it needs more explanation.

CMainFrame - Implements the main frame. Besides what was generated (adjusted to fit the application needs), I added code to deal with menu entries, some message routing to the view and that's about it. CMainFrame::OnFileOpen deals with opening a configuration xml file for the solar system. CMainFrame::OnViewFullscreen besides switching the full screen, it also hides the mfc toolbar that allows closing the full screen.

CSolarSystemApp - Implements the application object. Not much change there, besides setting a path for registry settings, setting the main window title and loading the ole libs (needed for MS xml parser)

CSolarSystemDoc - Contains and manages the computation thread (m_Thread) and the solar system data (m_SolarSystem). Most of the code deals with loading the xml file. There is also some code that deals with the computation thread.

CSolarSystemView - The view. Deals with OpenGL setup, displaying the scene and also with the keyboard and mouse message handling. CSolarSystemView::RenderScene is the drawing method. CSolarSystemView::OnDraw does the actual drawing, CSolarSystemView::RenderShadowScene is a quite simplified version of the previous, dealing only with shadows, CSolarSystemView::RenderSkybox draws the skybox.  CSolarSystemView::MoonHack is a 'moon hack' that allows rescaling of the distance between the planet and its moons (configurable in the xml file). It works for a reasonable number of bodies but there can be much better alternatives. It's good enough for the purpose of the project. CSolarSystemView::KeyPressHandler is the key press handler. For camera movement, it just sets a value, the timer handler takes care of the actual camera movement. Both CSolarSystemView::OnMouseWheel and CSolarSystemView::OnLButtonDown move the camera, but the actual movement happens when the timer handler calls camera.Tick(). CSolarSystemView::OnTimer is the timer handler. In there the computation thread is signaled to do more calculations and also movements happen.

The classes that deal with settings:

Options - This is the settings class. Load loads them from the registry, Save writes them into registry.

OptionsPropertySheet, DisplayPropertyPage, CameraPropertyPage - UI for the options. I think the names are descriptive enough and the implementation is pretty straightforward.

Some UI classes:

CEmbeddedSlider, CMFCToolBarSlider - Implement the slider from the toolbar that allows setting the simulation speed. I used a Microsoft sample as an example to implement them. For some reason, the classes from the sample didn't work for me 'out of the box' so I rewrote the classes using the ones from the sample as a guideline.

Solar system data:

SolarSystemBodies - just containing vectors of bodies and body properties. 

Body - Contains data used in calculations, except m_Radius, which is not really needed but I included it in case I'll use the code later and collisions will be involved.

BodyProperties - Contains information needed for displaying, like the texture.

Vector3D<T> - Used as Vector3D<double> in the code. One reason I did not use the glm vector was that I had some old code for the camera I wrote a while ago and it used this. Another reason is that I'll probably reuse it in other projects where glm will not be used.

ComputationThread - a thread doing calculations with VelocityVerlet. You may find quite a bit of description of it on the blog.

OpenGL classes:

There might be more description on the blog and I'll also add links to some OpenGL tutorials on the net, here is a very short description:

SolarSystemGLProgram - the GL program for displaying the solar system. Since it's quite customized I preferred to not include it in the OpenGL namespace. You'll find in there the vertex and fragment shaders. Lightning is Blinn-Phong and although it looks like point lightning at a quick look on the screen, it's actually directional lightning, just that the direction is changed for each object in the scene to be from the direction of the Sun (so it's some sort of a hybrid between directional and point lightning). It should work with more than one light source but I did not test it. Shadows are omnidirectional shadows using a depth cubemap (only for a single Sun, sorry). Unfortunately shading is behaving as for point lightning and it's not very realistic, just look at the shadows of planets thrown on other planets. It was the best I could do in short time. Since I mentioned the unrealistic shadows, lightning is also quite unphysical, the attenuation of light is linear with distance, instead of quadratic, but it looks nicer on screen.

The OpenGL namespace:

There are several classes in there which I prefer to not describe each (yet?) but I'll sketch the idea. They are some very simple wrappers for OpenGL API. OpenGLObject is an abstract class that is used as a base class for a lot of them, it just wraps an ID. The names should be quite descriptive, one should see immediately what SkyBoxCubeMapProgram or ShadowCubeMapProgram do. Camera is the camera class, I had it already written but with the old OpenGL fixed pipeline (using gluLookAt) and that's why it uses vector arithmetic - the Vector3D<double> mentioned above - instead of matrices or quaternions for rotations. There are some classes that are not used in this project, ComputeShader and MatrixPush, the first I might use in the future in other projects, the last one can be used in case you like the old glPushMatrix/glPopMatrix way. Cube is also not used. Sphere is obviously for drawing spheres and it's used for all the suns/planets/moons in the program. 


HOW TO USE

Start and stop the simulation pressing space or by clicking the 'run' toolbar button or use the menu entry. Load a different xml file using File | Open. Change settings with View | Options. Enter Full Screen with View | Full Screen, exit with the escape key. Turn the camera towards a point by clicking on it. Move the camera forward or backward by using up and down keys, or you can use the scroll mouse wheel. Keep shift pressed and the camera will move up or down instead of forward or backward. With control key pressed, it will rotate up or down (pitch up or down). Left and right arrows will move the camera towards left or right, unless the control key is pressed, when the camera will yaw left or right. With shift, it will roll left or right. You can increase/decrease the speed of the simulation using the slider on the toolbar or +/- keys.


LINKS

Those are some links to some OpenGL tutorials I visited while refreshing my memory about OpenGL and implementing the OpenGL code:

http://www.learnopengl.com/ ,

http://alfonse.bitbucket.org/oldtut/ ,

http://www.opengl-tutorial.org/ ,

http://www.tomdalling.com/blog/category/modern-opengl/
