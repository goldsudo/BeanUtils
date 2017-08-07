# BeanUtils

This BeanUtils library is a Java bean copy utility with powerful functionality and high performance.

## Features:
* support copy with Java primitive type auto-convert to its Java type. e.g. int <=> Integer
* support copy with array type. e.g. int[] <=> Integer[]
* support copy with Java Collection type. e.g. List<FromBean> => List<ToBean>
* support copy with property name mapping. e.g. int id => int userId
* support copy with recursion copy. 
* support custom data convert when coping.
* with performance as native copy.
* easy usage, annotation way to define the property mapping.

## A full Sample:

From class and To class:

	                                           @BeanCopySource(source=FromBean.class)          
	public class FromBean {                    public class ToBean {                           
	                                                                                       
		private boolean beanBool;              private Boolean beanBool;                   
		private byte beanByte;                 private Byte beanByte;                      
		private char beanChar;                 private Character beanChar;                 
		private short beanShort;               private Short beanShort;                    
		private int beanInt;                   private Integer beanInt;                    
		private Long beanLong;                 private long beanLong;                      
		private Float beanFloat;               private float beanFloat;                    
		private Double beanDouble;             private double beanDouble;                  
		private String beanString;             private String beanString;                  
		                                                                                   
		                                       @CopyProperty(convertor=DateConvertor.class)
		private Date beanDate;                 private String beanDate;                    
		                                                                                   
		private int[] beanIntArray;            private int[] beanIntArray;                 
		                                                                                   
		                                       @CopyProperty                               
		private FromBean2 bean2;               private ToBean2 bean2;                      
		                                                                                   
		                                       @CopyCollection(targetClass=ToBean3.class)  
		private List<FromBean3> bean3List;     private List<ToBean3> bean3List;            
		                                                                                   
		                                       @CopyProperty                               
		private FromBean4[] bean4Array;        private ToBean4[] bean4Array;               
		                                                                                   
		// getters and setters...              @CopyProperty(property="beanInt")           
	}                                              private int beanId;                         
	                                                                                       
	                                               @CopyProperty(property="bean2.beanString")  
	                                               private String bean2String;                 
	                                           
	                                               // getters and setters...
	                                           }

And one line code as:

	ToBean toBean = BeanCopyUtils.copyBean(fromBean, ToBean.class);

## Performance:
|Library|1 time|100 times|10000 times|1000000 times|10000000 times|
|-|-|-|-|-|
|org.apache.commons.beanutils.BeanUtil.copyProperties|1|12|128|9963|99879|
|org.apache.commons.beanutils.PropertyUtils.copyProperties|0|2|56|5564|55651|
|org.springframework.beans.BeanUtils.copyProperties|0|2|5|473|4700|
|org.springframework.cglib.beans.BeanCopier.create 1|1|2|2|87|843|
|org.springframework.cglib.beans.BeanCopier.create 2|0|0|0|10|98|
|com.tuyang.beanutils.BeanCopyUtils.copyBean 1|0|1|0|21|196|
|com.tuyang.beanutils.BeanCopyUtils.copyBean 2|0|0|0|11|97|
|native Copy|0|0|0|10|88|


## API Usage:
	FromBean fromBean = ...;
	ToBean toBean = BeanCopyUtils.copyBean(fromBean, ToBean.class);

or:

	List<FromBean> fromList = ...;
	List<ToBean> toList = BeanCopyUtils.copyList(fromList, ToBean.class);
	
or:

	BeanCopier copier = BeanCopyUtils.getBeanCopier(FromBean.class, ToBean.class);
	ToBean toBean = new ToBean();
	toBean = (ToBean)copier.copyBean(fromBean, toBean);

or with option class, when ToBean class cannot be modified. At this time ToBeanOption class acts as a configuration class of ToBean class, and annotations are configured in this class:

	FromBean fromBean = ...;
	ToBean toBean = BeanCopyUtils.copyBean(fromBean, ToBean.class, ToBeanOption.class);
	
	@BeanCopySource(source=FromBean.class)
	public class ToBeanOption {
		
		@BeanCopy(property="beanInt")
		private int beanId;
		...
		

## Annotation Detail
### BeanCopySource:
	//use @BeanCopySource to specify the source class.
	@BeanCopySource(source=FromBean.class)
	public class ToBean {
		...
	}
	
### CopyProperty:

#### default:
Used to specify the property should be copied to. e.g.

	@CopyProperty
	private ToBean2 bean2;

#### name mapping

Annotation CopyProperty's 'property' property is used for name mapping as following:

	@CopyProperty(property="beanInt")
	private Integer toInt;
	
	@CopyProperty(property="bean2.beanInt")
	private int bean2Int;
	
#### ignore
Annotation CopyProperty's 'ignored' property is used for ignoring the property when coping.

	@CopyProperty(ignored=true)
	private Integer beanInt;

#### convertor
Annotation CopyProperty's 'convertor' property is used for custom data converting.

	@CopyProperty(convertor=DateConvertor.class)
	private String beanDate;

