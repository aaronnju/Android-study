


	public class ReflectTest {
	    private int mInt = 100;
	    private int test(){
	        return mInt * 2;
	    }
	    public static void main(String[] args){
	        try {
	            Class<?> obj = null;
	            obj = Class.forName("ReflectTest");
	            Field[] f = obj.getDeclaredFields();
	            for(Field field : f){
	                field.setAccessible(true);
	                System.out.println(field.getName()+":"+field.get(obj.newInstance()));
	            }
	
	            ReflectTest privateObject = new ReflectTest();
	            Field privateStringField = ReflectTest.class.getDeclaredField("mInt");
	            privateStringField.setAccessible(true);//允许访问私有字
	            privateStringField.setInt(privateObject, 120);
	            System.out.println("fieldValue = " + (int) privateStringField.get(privateObject));
	
	            Method privateStringMethod = ReflectTest.class.getDeclaredMethod("test", null);
	            privateStringMethod.setAccessible(true);
	            System.out.println("returnValue = " + privateStringMethod.invoke(privateObject, null));
	        } catch (ClassNotFoundException e) {
	            e.printStackTrace();
	        }catch (InstantiationException e) {
	            e.printStackTrace();
	        } catch (IllegalAccessException e) {
	            e.printStackTrace();
	        } catch (NoSuchFieldException e) {
	            e.printStackTrace();
	        } catch (NoSuchMethodException e) {
	            e.printStackTrace();
	        } catch (InvocationTargetException e) {
	            e.printStackTrace();
	        }
	    }
	}