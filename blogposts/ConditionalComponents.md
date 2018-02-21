# Conditional component rendering in React

When creating generic, reusable components in React, it is always important to make these components stateless.  As you work with stateless React components, you will come across a common pattern where components conditionally render.  For instance, if you were create a simple stateless flash message component like this:

	const FlashMessage = ({ message }) => (
	  <div className="flash-message">
	    {message}
	  </div>
	)

You would only want the flash message to render when there is a message that you want to show the user.  If you were approaching this from JQuery mindset, you would probably pass in a flag and have it change a style.  Like this:

	const FlashMessage = ({ message, showFlash }) => {
	  const isFlashVisible = () = {
		if(showFlash){
			return 'block'
		}else{
			return 'none'
		}
      } 
	  return (
	    <div className="flash-message" style={{display: isFlashVisible()}}>
	      {message}
	    </div>
	  )
	}

### Conditional Rendering
However, with React we can make things a lot simpler.  Instead of creating the DOM node and then  hiding or showing it, we can simple just never render the component.  Not only is this more efficient, it is actually easier to:

	const FlashMessage = ({ message, showFlash }) => {
	  if(showFlash){
	    return (
	      <div className="flash-message">
	        {message}
		  </div>
		)
	  }else{
        return null
      }
	}

### Ternaries
React requires that we return something so, in this case, we are returning the flash component when `showFlash` is true and `null`, which doesn't get rendered, if it is false.  However, we could clean this up a bit.  Ternaries are permitted in explicit returns, so we could trim a lot of this code down.

	const FlashMessage = ({ message, showFlash }) => (
	  showFlash 
	    ? <div className="flash-message">
	        {message}
		  </div>
		: null
	)

### Single Source of Truth
That is definitely cleaner, but we have a pretty glaring issue.  There are two sources of truth in this component.  A developer could pass in a true `showFlash` flag and no message and still end up with a blank flash message.  In general, it is always better to base your logic directly on the data that it is dependent on.  In this case, since an empty `message` string, undefined value, or null value are all falsey, we can clean up our code even more by basing things off the `message` while avoiding the potential for a future bug. 

	const FlashMessage = ({ message }) => (
	  message 
	    ? <div className="flash-message">
	        {message}
		  </div>
		: null
	)

### AND Operator
We have stripped out the need for additional style logic, removed a second source of truth, and cleaned up our code substantially.  Although, I don't know about you but, to me, that ` null` hanging on the end there feels less than elegant.  Luckily, since the component should only render when message is truthy, we can use the AND operator (`&&`) to help clean that up.

	const FlashMessage = ({ message }) => (
	  message 
	    && <div className="flash-message">
	         {message}
		   </div>
	)

This is definitely looking better. Although, there is still technically a chance for a bug here that is worth nipping in the bud before releasing this to production.   

### Bug Bash
React treats a prop passed without a value as a true Boolean, like if you were to put the `checked` attribute on a checkbox HTML element.  In this case, if someone were to write this: `<FlashMessage message />`, message would be truthy, and we would end up rendering an empty flash message.  
Do we want that to happen? No.  
Is it likely to happen?  No.  
Is it a potential bug that is easy to fix? Yes.  
So let's take a little extra time and do this right.

The fix is pretty simple, we just need to confirm that our message is not a Boolean.  More accurately, we want to assure that message is of the type `string`.

	const FlashMessage = ({ message }) => (
	  typeof message === 'string' && message 
	    && <div className="flash-message">
	         {message}
		   </div>
	)

Looks surprisingly similar to our original non-conditional component, doesn't it?  That is the nice thing about React, it makes it easy to show and hide components with just a few extra characters.

TL;DR
- Stateless React components are typically more reusable.
- Instead of changing a style or class to hide a component, use conditional logic to not render it.
- Look out for multiple sources of truth.  Don't create config flags if you can avoid them.
- Consider ternaries when rendering two different options and the AND operator when using a Boolean to decide if to show a single option.
- Remember that React treats props without a specified value as a true Boolean.
