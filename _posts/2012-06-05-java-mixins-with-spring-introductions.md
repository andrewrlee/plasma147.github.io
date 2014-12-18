---
layout: post
title: Java mixins with Spring Introductions
category: Software
tags: [Spring, AOP, Mixins, Java, Composite]
date: 2012-06-05
---

<p>Creating java mixins with Spring introductions.</p>

<p>At work we have a quite complex, sprawling domain model. For test purposes we had created multiple helper classes that were each responsible for data generation in different areas of the domain. </p> 
<p>As integration tests often spanned multiple domain areas then the convenience, a class was created that implemented all of the data helper interfaces and then delegated down to the datahelper implementations. This merged dependencies on multiple classes into one. 
</p>
<p>This class was horrific: ~3,500 lines of boiler plate code plus a few places where developers had added helper methods as they couldn't be bothered to add it to a specific datahelper implementation and thread it through.</p>       
<p>In other languages this boilerplate code could be eliminated through the use of mixins - We would have one base class and then mixin in the functionality of each of the other datahelper implementations.</p>      

<p>We can simulate this in java through the use of Spring Introductions. The idea is that a proxy gets created for a specified interface and then one or more classes that provide some of the functionality behind this interface gets added as an "introduction advisor". When a method is called on the proxy - spring routes the invocation to the implementation class.</p>       

<p>The implementation of a class that builds these kinds of the proxies can be seen below: 

<pre class="prettyprint linenums">

package uk.co.optimisticpanda.spring.proxy;

import java.util.ArrayList;
import java.util.List;

import org.springframework.aop.Advisor;
import org.springframework.aop.framework.ProxyFactory;
import org.springframework.aop.support.DefaultIntroductionAdvisor;
import org.springframework.aop.support.DelegatingIntroductionInterceptor;

public class Composite&lt;D&gt;{

	private List&lt;Object&gt; instances = new ArrayList&lt;Object&gt;();
	private Class&lt;D&gt; returnType;
	
	private Composite(Class&lt;D&gt; returnType){
		this.returnType = returnType;
	}
	
	private Composite(){
		this(null);
	}
	
	public static &lt;A&gt; Composite&lt;A&gt; create(Class&lt;A&gt; a){
		return new Composite&lt;A&gt;(a);
	}
	
	@SuppressWarnings("rawtypes")
	public static Composite&lt;?&gt; create(){
		return new Composite();
	}
	
	public Composite&lt;D&gt; mixin(Object... o){
		for (Object object : o) {
			instances.add(object);
		}
		return this;
	}
	
	@SuppressWarnings("unchecked")
	public D build(){
		ProxyFactory factory = new ProxyFactory();
		
		if(instances.isEmpty()){
			throw new RuntimeException("Need to specify at least one implementation to mixin");
		}
		
		for (Object instance  : instances) {
			factory.addAdvisor(buildAdvisor(instance));
		}

		if(returnType != null){
			factory.addInterface(returnType);
		}
		return (D) factory.getProxy();
	}
	
	private Advisor buildAdvisor(Object instance){
		DelegatingIntroductionInterceptor interceptor = new DelegatingIntroductionInterceptor(instance);
		DefaultIntroductionAdvisor advisor = new DefaultIntroductionAdvisor(interceptor);
		return advisor;
	}
	
 }

</pre>

<p>The below unit test shows how this is used:</p> 

<pre class="prettyprint linenums">
package uk.co.optimisticpanda.spring.proxy;

import junit.framework.TestCase;


public class CompositeTest extends TestCase{

	private IA a;
	private IB b;

	@Override
	protected void setUp() throws Exception {
		a = new IA(){
			@Override
			public String getA() {
				return "a";
			}};
		b = new IB() {
			
			@Override
			public String getB() {
				return "b";
			}};
	}

	public void testNonTypedComposite(){
		Object proxy = Composite.create().mixin(a,b).build();
		
		assertEquals("a", ((IA)proxy).getA());
		assertEquals("b", ((IB)proxy).getB());
	}
	
	public void testTypeSafeComposite(){
		IAandB proxy = (IAandB) Composite.create(IAandB.class).mixin(a,b).build();
		
		assertEquals("a", proxy.getA());
		assertEquals("b", proxy.getB());
	}
	
	public void testTypeMustBeInterface(){
		try{
			Composite.create(Object.class).mixin(a,b).build();
			fail("Should fail as object is not interface");
		}catch(IllegalArgumentException e){
			assertEquals("[java.lang.Object] is not an interface", e.getMessage());
		}
	}

	public void testNeedToSpecifyImplementations(){
		try{
			Composite.create().build();
			fail("Should fail as no interfaces specified");
		}catch(RuntimeException e){
			assertEquals("Need to specify at least one implementation to mixin", e.getMessage());
		}
	}


	public interface IA {
		String getA();
	}

	public interface IB {
		String getB();
	}
	public interface IAandB extends IA,IB {	}

}
</pre>

<p>With this class, I was able to create one interface that extended all of the existing datahelper interfaces. I then mixed in all of the existing datahelper implementations and created a composite as the shared interface. 
This was a nice way of getting rid of 3.5K lines of code but there are deeper issues at hand that lead to this in the first place. The existing class was a bit of a god object and this has just been a nifty way to replace it with something slightly more maintainable.
</p>