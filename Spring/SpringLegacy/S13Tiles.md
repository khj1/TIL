# Spring - tiles
**스프링 타일즈**란 뷰에서 jsp들의 상단, 사이드, 메인, 하단을 설정 상태로 include 처리해주는 구조의 템플릿을 말한다. 페이지들을 **일괄관리** 할 수 있고, 공통사용하는 부분들을 **매번 등록을 따로 해주지 않아도** 되기 때문에 편리하다.

## 기본 설정
> ### pom.xml
```xml
<dependency>
    <groupId>org.apache.tiles</groupId>
    <artifactId>tiles-core</artifactId>
    <version>3.0.3</version>
</dependency>      
<dependency>
    <groupId>org.apache.tiles</groupId>
    <artifactId>tiles-servlet</artifactId>
    <version>3.0.3</version>
</dependency>      
<dependency>
    <groupId>org.apache.tiles</groupId>
    <artifactId>tiles-jsp</artifactId>
    <version>3.0.3</version>
</dependency>      
<dependency>
    <groupId>org.apache.tiles</groupId>
    <artifactId>tiles-extras</artifactId>
    <version>3.0.3</version>
</dependency>
```
> ### servlet-context.xml
```xml
<!-- 
    프로젝트 생성 시 기본적으로 제공되는 ViewResolver로 컨트롤러가 반환하는
    뷰의 경로를 조립하는 역할을 담당한다. 타일즈의 ViewResolver가 먼저 호출
    될 수 있도록 order 프로퍼티를 추가하여 2로 설정한다.
-->
<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <beans:property name="prefix" value="/WEB-INF/views/" />
    <beans:property name="suffix" value=".jsp" />
    <beans:property name="order" value="2" />
</beans:bean>

<!-- Maven : spring-webmvc.jar -->
<!-- 
    타일즈에서 제공하는 ViewResolver가 먼저 호출되게 하여 템플릿을 통한
    레이아웃을 먼저 설정할 수 있도록 해준다. 따라서 order 프로퍼티를 1로 설정한다.
-->
<beans:bean id="tilesViewResolver" class="org.springframework.web.servlet.view.UrlBasedViewResolver">
    <beans:property name="viewClass" value="org.springframework.web.servlet.view.tiles3.TilesView" />
    <beans:property name="order" value="1" />
</beans:bean>
<!-- 타일즈 설정파일의 위치를 설정한다. -->
<beans:bean id="tilesConfigurer" class="org.springframework.web.servlet.view.tiles3.TilesConfigurer">
    <beans:property name="definitions">
        <beans:list>
            <beans:value>/WEB-INF/spring/tiles-define.xml</beans:value>
        </beans:list>
    </beans:property>
</beans:bean>
```
> ### 타일즈 설정 파일 - tiles-define.xml( 임의로 작성 가능 )
```xml
<!-- 일반적인 xml 파일로 생성한 후 DOCTYPE만 추가하면 된다.-->
<!DOCTYPE tiles-definitions PUBLIC
       "-//Apache Software Foundation//DTD Tiles Configuration 3.0//EN"
       "http://tiles.apache.org/dtds/tiles-config_3_0.dtd">

<!-- 
	상위 <definition - 태그에서 템플릿의 레이아웃으로 사용할 JSP 파일을 설정
			<put-attribute - 태그에서 각 위치의 JSP 파일을 설정
			
	하위 <definition 태그에서 요청명의 패턴에 따른 설정을 한다.
		상위 태그에서의 name 속성 값과 하위 태그에서의 extends의 속성값이 일치해야한다.
-->
<tiles-definitions>
	<definition name="layout-tiles" template="/WEB-INF/views/tiles/tiles_layout.jsp">
		<put-attribute name="top" value="/WEB-INF/views/tiles/topTemplate.jsp" />
		<put-attribute name="left" value="/WEB-INF/views/tiles/leftTemplate.jsp" />
		<put-attribute name="body" value="" />
		<put-attribute name="bottom" value="/WEB-INF/views/tiles/bottomTemplate.jsp" />
	</definition>
	<definition name="*/*" extends="layout-tiles">
		<put-attribute name="body" value="/WEB-INF/views/{1}/{2}.jsp"/>
		<put-attribute name="title" value="타일즈 적용"/>
	</definition>
</tiles-definitions>
```

***
## 타일즈 사용하기
타일즈 설정파일에서 ```<definition>``` 태그의 ```template``` 속성에 지정된 경로의 파일을 템플릿 레이아웃으로 사용하게 된다.
> ### 템플릿 레이아웃 파일( 해당 예제: tiles_layout.jsp )
```xml
<div class="container">
    <div class="wrap">
        <tiles:insertAttribute name="top"/>
        <div class="content">
            <tiles:insertAttribute name="left"/>
            <div class="page_content">
                <!-- 
                    해당 속성에 아무런 값이 없으면 에러가 발생한다 
                    이를 방지해주기 위해 ignore="true"를 입력해준다. 
                -->
                <tiles:insertAttribute name="body" ignore="true"/>
            </div>
        </div>
        <tiles:insertAttribute name="bottom"/>
    </div>
</div>
```