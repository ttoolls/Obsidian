In file included from /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/Object.hpp:36,

[build]                  from /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/Types.hpp:28,

[build]                  from /home/anatolii/Projects/Abus/aoc-device/code/oatpp-common/include/common/dto/status_dto.h:29,

[build]                  from /home/anatolii/Projects/Abus/aoc-device/code/endpoint_services/debug_endpoints/service/include/debug_endpoints/service/debug_endpoints_service.h:30,

[build]                  from /home/anatolii/Projects/Abus/aoc-device/code/endpoint_services/debug_endpoints/service/src/debug_endpoints_service.cc:1:

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/./Vector.hpp: In instantiation of 'static oatpp::data::mapping::type::Type oatpp::data::mapping::type::__class::Vector<T>::createType() [with T = Single_sql_response]':

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/./Vector.hpp:116:36:   required from 'static oatpp::data::mapping::type::Type* oatpp::data::mapping::type::__class::Vector<T>::getType() [with T = Single_sql_response]'

[build] /home/anatolii/Projects/Abus/aoc-device/code/endpoint_services/debug_endpoints/dto/include/debug_endpoints/dto/debug_endpoints_dto.h:71:5:   required from here

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/./Vector.hpp:107:46: error: no type named 'Class' in 'class Single_sql_response'

[build]   107 |       info.params.push_back(T::Class::getType());

[build]       |                             ~~~~~~~~~~~~~~~~~^~

[build] In file included from /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/Object.hpp:28,

[build]                  from /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/Types.hpp:28,

[build]                  from /home/anatolii/Projects/Abus/aoc-device/code/oatpp-common/include/common/dto/status_dto.h:29,

[build]                  from /home/anatolii/Projects/Abus/aoc-device/code/endpoint_services/debug_endpoints/service/include/debug_endpoints/service/debug_endpoints_service.h:30,

[build]                  from /home/anatolii/Projects/Abus/aoc-device/code/endpoint_services/debug_endpoints/service/src/debug_endpoints_service.cc:1:

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/./Type.hpp: In instantiation of 'Wrapper oatpp::data::mapping::type::ObjectWrapper<T, Clazz>::cast() const [with Wrapper = Single_sql_response; T = void; Clazz = oatpp::data::mapping::type::__class::Void]':

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/././Collection.hpp:174:64:   required from 'void oatpp::data::mapping::type::__class::StandardCollection<ContainerType, ItemType, Clazz>::PolymorphicDispatcher::addItem(const oatpp::data::mapping::type::Void&, const oatpp::data::mapping::type::Void&) const [with ContainerType = std::vector<Single_sql_response, std::allocator<Single_sql_response> >; ItemType = Single_sql_response; Clazz = oatpp::data::mapping::type::__class::Vector<Single_sql_response>]'

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/././Collection.hpp:172:10:   required from here

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/./Type.hpp:559:30: error: no type named 'Class' in 'class Single_sql_response'

[build]   559 |   if(!Wrapper::Class::getType()->extends(m_valueType)) {

[build]       |       ~~~~~~~~~~~~~~~~~~~~~~~^~

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/./Type.hpp:560:31: error: no type named 'Class' in 'class Single_sql_response'

[build]   560 |     if(Wrapper::Class::getType() != __class::Void::getType() && m_valueType != __class::Void::getType()) {

[build]       |        ~~~~~~~~~~~~~~~~~~~~~~~^~

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/./Type.hpp:563:67: error: no type named 'Class' in 'class Single_sql_response'

[build]   563 |                                std::string(Wrapper::Class::getType()->classId.name) + "'.");

[build]       |                                            ~~~~~~~~~~~~~~~~~~~~~~~^~

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/./Type.hpp:566:23: error: no type named 'ObjectType' in 'class Single_sql_response'

[build]   566 |   return Wrapper(std::static_pointer_cast<typename Wrapper::ObjectType>(m_ptr), Wrapper::Class::getType());

[build]       |                  ~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/./Type.hpp:566:104: error: no type named 'Class' in 'class Single_sql_response'

[build]   566 |   return Wrapper(std::static_pointer_cast<typename Wrapper::ObjectType>(m_ptr), Wrapper::Class::getType());

[build]       |                                                                                 ~~~~~~~~~~~~~~~~~~~~~~~^~

[build] In file included from /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/./List.hpp:28,

[build]                  from /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/Object.hpp:35,

[build]                  from /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/Types.hpp:28,

[build]                  from /home/anatolii/Projects/Abus/aoc-device/code/oatpp-common/include/common/dto/status_dto.h:29,

[build]                  from /home/anatolii/Projects/Abus/aoc-device/code/endpoint_services/debug_endpoints/service/include/debug_endpoints/service/debug_endpoints_service.h:30,

[build]                  from /home/anatolii/Projects/Abus/aoc-device/code/endpoint_services/debug_endpoints/service/src/debug_endpoints_service.cc:1:

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/././Collection.hpp: In instantiation of 'oatpp::data::mapping::type::Void oatpp::data::mapping::type::__class::StandardCollection<ContainerType, ItemType, Clazz>::Iterator::get() [with ContainerType = std::vector<Single_sql_response, std::allocator<Single_sql_response> >; ItemType = Single_sql_response; Clazz = oatpp::data::mapping::type::__class::Vector<Single_sql_response>]':

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/././Collection.hpp:139:16:   required from here

[build] /opt/poky/active/sysroots/cortexa53-poky-linux/usr/include/oatpp-1.3.0/oatpp/oatpp/core/data/mapping/type/././Collection.hpp:140:15: error: could not convert '((oatpp::data::mapping::type::__class::StandardCollection<std::vector<Single_sql_response, std::allocator<Single_sql_response> >, Single_sql_response, oatpp::data::mapping::type::__class::Vector<Single_sql_response> >::Iterator*)this)->oatpp::data::mapping::type::__class::StandardCollection<std::vector<Single_sql_response, std::allocator<Single_sql_response> >, Single_sql_response, oatpp::data::mapping::type::__class::Vector<Single_sql_response> >::Iterator::iterator.__gnu_cxx::__normal_iterator<Single_sql_response*, std::vector<Single_sql_response, std::allocator<Single_sql_response> > >::operator*()' from 'Single_sql_response' to 'oatpp::data::mapping::type::Void'

[build]   140 |       return *iterator;

[build]       |               ^~~~~~~~

[build]       |               |

[build]       |               Single_sql_response

[build] make[3]: *** [code/endpoint_services/debug_endpoints/CMakeFiles/debug_endpoints-dal.dir/build.make:76: code/endpoint_services/debug_endpoints/CMakeFiles/debug_endpoints-dal.dir/service/src/debug_endpoints_service.cc.o] Error 1

[build] make[2]: *** [CMakeFiles/Makefile2:12204: code/endpoint_services/debug_endpoints/CMakeFiles/debug_endpoints-dal.dir/all] Error 2

[build] make[1]: *** [CMakeFiles/Makefile2:12255: code/endpoint_services/debug_endpoints/CMakeFiles/debug_endpoints.dir/rule] Error 2

[build] make: *** [Makefile:3060: debug_endpoints] Error 2

[proc] The command: /usr/local/bin/cmake --build /home/anatolii/Projects/Abus/aoc-device/build --config Debug --target debug_endpoints -j 10 -- exited with code: 2

[driver] Build completed: 00:00:01.863