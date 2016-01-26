title: 关于我
date: 2015-10-14 15:14:25
---


    class Coder {
      protected $name = '张亚可';
      protected $age = '24';
      protected $sex = '男';
      protected $job = '软件开发';
      protected $languages = ['PHP', 'Nodejs'];
      protected $DBs = ['MySQL', 'MongoDB'];
      function __construct($name, $age, $sex) {
        $this->name = $name;
        $this->age = $age;
        $this->sex = $sex;
      }
    }


<script>
  var x, y, a;
for (y = 1.5; y > -1.5; y -= 0.1) {
    var str = '';
    for (x = -1.5; x < 1.5; x += 0.05) {
        a = x * x + y * y - 1;
        str += (a * a * a - x * x * y * y * y <= 0.0 ? '*' : ' ');
    }
    console.log(str);
}
</script>