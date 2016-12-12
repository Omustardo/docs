-   http://www.opengl-tutorial.org/
    Excellent.  

-   [open.gl](<https://open.gl/>)
    Amazing general resource. Good explanations.

-   http://www.creativebloq.com/javascript/get-started-webgl-draw-square-7112981
    Intro to WebGL, but really good for general OpenGL, shaders, and more.

-   https://raw.githubusercontent.com/go-gl/gl/master/v2.1/gl/package.go For
    OpenGL in golang. Handy for looking up functions and constants quickly.

-   http://stackoverflow.com/questions/21652546/what-is-the-role-of-glbindvertexarrays-vs-glbindbuffer-and-what-is-their-relatio
    "A buffer object in OpenGL is a big blob of bits. Think of the "active"
    buffer as just a global variable, and there are a bunch of functions which
    use the active buffer instead of using a parameter."

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
GLuint array; 
// Generate a name for a new array. 
glGenVertexArrays(1, &array); 
// Make the new array active, creating it if necessary. 
glBindVertexArray(array);
// Make the buffer the active array buffer. 
glBindBuffer(GL_ARRAY_BUFFER, buffer); 
// Attach the active buffer to the active array, 
// as an array of vectors with 4 floats each. 
// Kind of like: 
// opengl->current_vertex_array->attributes[attr] = { 
// type = GL_FLOAT, 
// size = 4, 
// data = opengl->current_array_buffer 
// } glVertexAttribPointer(attr, 4, GL_FLOAT, GL_FALSE, 0, 0); 
// Enable the vertex attribute glEnableVertexAttribArray(attr);
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-   http://stackoverflow.com/questions/19102180/how-does-gldrawarrays-know-what-to-draw
    The call to glBindBuffer tells OpenGL to use vertexBufferObject whenever it
    needs the GL\_ARRAY\_BUFFER.
    glEnableVertexAttribArray means that you want OpenGL to use vertex attribute
    arrays; without this call the data you supplied will be ignored.
    glVertexAttribPointer, as you said, tells OpenGL what to do with the
    supplied array data, since OpenGL doesn't inherently know what format that
    data will be in.
    glDrawArrays uses all of the above data to draw points.
    Remember that OpenGL is a big state machine. Most calls to OpenGL functions
    modify a global state that you can't directly access. That's why the code
    ends with glDisableVertexAttribArray and glBindBuffer(..., 0): you have to
    put that global state back when you're done using it.

-   http://stackoverflow.com/questions/32915543/will-memory-leak-if-do-multiple-glbindbuffers-on-same-buffer-object-before-call?rq=1  
      
    There are 3 separate entities in play here:
    - A buffer **name**. Since it is an integer value lot of people like to call
    this an "id". But if you read official OpenGL documentation, it is always
    called "name".
    - A buffer **object**. This is the object that contains the state
    attributes, like the current size, the usage flags, etc.
    - A buffer **store**. This is the memory that contains the actual buffer
    content. With these definitions, we can describe what each call is doing:  
      
    glGenBuffers() creates buffer names.
    glBindBuffer() creates a buffer object the first time the given name is
    bound, and makes it the current buffer for other calls operating on buffers.
    glBufferData() deletes the current buffer store if there already was one,
    and creates a new buffer store.
    glBufferSubData() modifies data in the existing data store.
    glDeleteBuffers() deletes the buffer names, as well as the associated buffer
    objects and buffer stores if they exist. This means that calling
    glBufferData() on the same buffer object multiple times is perfectly fine,
    and will not cause any memory leaks.  
      
    A subtle detail in the list above is that glGenBuffers() does not create
    buffer objects. That only happens the first time the name is bound. This
    does not really matter for buffer objects, but it is important to understand
    for other types of objects (like textures) that have the same behavior.

- Vertex buffer objects (VBOs) are different entities than Vertex array objects (VAOs). VBOs allow you to store batch data in driver memory rather than application memory, whereas VAOs capture the bindings end enables used for submitting batch data.