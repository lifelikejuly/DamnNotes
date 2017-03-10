# 问题
使用Realm数据时，设置Models时遇到一个问题City、province、Street、District四个类具有相同的几个参数，但属于不同表内容。开始我想使用继承extends一个父类Area
```
public class City extend Area {

}

public class Area extend RealmObject{
    public String text;
    public String code;
}
```
然后编译不通过，Realm不允许数据表RealmObject使用继承。那么使用接口形式总可以吧。
```
@RealmClass
public class City implment Area,RealmModle {

}

public interface Area {
    String text = null;
    String code = null;
}
```
编译还是没有通过，报错信息：
```
Android Error:Execution failed for task ':app:compileDebugJavaWithJavac'
```
# 解决办法
最终还是要在每个model中定义相同的参数（原来就是为了偷懒不想在每个model做重复的事），实现Area接口
```
public class City extends RealmObject implements Area{

    public String text;
    @PrimaryKey
    public String code;

    @Override
    public String getText() {
        return text;
    }

    @Override
    public String getCode() {
        return code;
    }
}

public interface Area  {


    String getText();

    String getCode();

}
```
但在数据集合取值时还是可以统一使用Area接口取值
```
public class AreasAdapter extends BaseAdapter<Area, AreasAdapter.AreasViewHolder> {


    public AreasAdapter(List<Area> datas) {
        super(datas);
    }

    @Override
    protected int getLayout() {
        return R.layout.item_area;
    }

    @Override
    protected AreasViewHolder newViewHolder(View view) {
        return new AreasViewHolder(view);
    }

    @Override
    protected void bindViewHolder(Area data, AreasViewHolder holder, int position) {
        holder.tvAddress.setText(data.getText());
    }

    class AreasViewHolder extends BaseAdapter.BaseViewHolder {
        @BindView(R.id.tv_address)
        TextView tvAddress;
        public AreasViewHolder(View itemView) {
            super(itemView);
        }
    }
}

RealmQuery<City> query = realm.where(City.class).like("code",province + "*");
RealmResults<City> cities = query.findAll();
adapter.setDatas(new ArrayList<Area>(cities));
```
折腾了半天就是为了少些几个类。