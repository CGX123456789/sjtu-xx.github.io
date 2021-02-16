---
title: vtk相关
toc: true
date: 2020-05-08 09:15:43
tags:
categories:
- 材料
---

# Observer的使用
<!--more-->
```c++
#include <vtkPolyDataMapper.h>
#include <vtkObjectFactory.h>
#include <vtkCommand.h>
#include <vtkActor.h>
#include <vtkSmartPointer.h>
#include <vtkRenderWindow.h>
#include <vtkRenderer.h>
#include <vtkRenderWindowInteractor.h>
#include <vtkInteractorStyle.h>
#include <vtkPolyData.h>
#include <vtkSphereSource.h>
#include <vtkInteractorStyleTrackballCamera.h>

// A class not derived from vtkObjectBase
class MyClass
{
  public:
    void KeypressCallbackFunction(vtkObject*,
                                  long unsigned int vtkNotUsed(eventId),
                                  void* vtkNotUsed(callData))
  {
    std::cout << "Caught event in MyClass" << std::endl;
  }

};

// A class that is derived from vtkObjectBase
class MyInteractorStyle : public vtkInteractorStyleTrackballCamera
{
public:
  static MyInteractorStyle* New();
  vtkTypeMacro(MyInteractorStyle, vtkInteractorStyleTrackballCamera);

  void KeypressCallbackFunction(vtkObject*,
                                long unsigned int vtkNotUsed(eventId),
                                void* vtkNotUsed(callData) )
  {
    std::cout << "Caught event in MyInteractorStyle" << std::endl;
  }

};
vtkStandardNewMacro(MyInteractorStyle);

int main(int, char *[])
{
  // Create a sphere
  vtkSmartPointer<vtkSphereSource> sphereSource =
    vtkSmartPointer<vtkSphereSource>::New();
  sphereSource->SetCenter(0.0, 0.0, 0.0);
  sphereSource->SetRadius(5.0);
  sphereSource->Update();

  vtkSmartPointer<vtkPolyDataMapper> mapper =
    vtkSmartPointer<vtkPolyDataMapper>::New();
  mapper->SetInputConnection(sphereSource->GetOutputPort());

  // Create an actor
  vtkSmartPointer<vtkActor> actor =
    vtkSmartPointer<vtkActor>::New();
  actor->SetMapper(mapper);

  // A renderer and render window
  vtkSmartPointer<vtkRenderer> renderer =
    vtkSmartPointer<vtkRenderer>::New();
  vtkSmartPointer<vtkRenderWindow> renderWindow =
    vtkSmartPointer<vtkRenderWindow>::New();
  renderWindow->AddRenderer(renderer);

  // An interactor
  vtkSmartPointer<vtkRenderWindowInteractor> renderWindowInteractor =
    vtkSmartPointer<vtkRenderWindowInteractor>::New();
  renderWindowInteractor->SetRenderWindow(renderWindow);

  MyClass myClass;
  renderWindowInteractor->AddObserver(vtkCommand::KeyPressEvent, &myClass, &MyClass::KeypressCallbackFunction);

  MyInteractorStyle* style = MyInteractorStyle::New();
  renderWindowInteractor->AddObserver(vtkCommand::KeyPressEvent, style, &MyInteractorStyle::KeypressCallbackFunction);

  vtkSmartPointer<MyInteractorStyle> style2 =
    vtkSmartPointer<MyInteractorStyle>::New();
  renderWindowInteractor->AddObserver(vtkCommand::KeyPressEvent, style2, &MyInteractorStyle::KeypressCallbackFunction);
  
  renderer->AddActor(actor);
  renderer->SetBackground(1,1,1); // Background color white
  renderWindow->Render();
  renderWindowInteractor->Start();

  style->Delete();

  return EXIT_SUCCESS;

```

# cell的拾取
```c++

#include <vtkAutoInit.h>
VTK_MODULE_INIT(vtkRenderingOpenGL)
VTK_MODULE_INIT(vtkInteractionStyle)
VTK_MODULE_INIT(vtkRenderingFreeType)
 
#include <vtkSmartPointer.h>
#include <vtkSphereSource.h>
#include <vtkPolyDataMapper.h>
#include <vtkActor.h>
#include <vtkProperty.h>
#include <vtkRenderer.h>
#include <vtkRenderWindow.h>
#include <vtkRenderWindowInteractor.h>
 
#include <vtkInteractorStyleTrackballCamera.h>
#include <vtkDataSetMapper.h>
#include <vtkCellPicker.h>
#include <vtkSelectionNode.h>
#include <vtkSelection.h>
#include <vtkRendererCollection.h>
#include <vtkExtractSelection.h>
#include <vtkObjectFactory.h>
 
/**************************************************************************/
class CellPickerInteractorStyle :public vtkInteractorStyleTrackballCamera
{
public:
	static CellPickerInteractorStyle* New();
 
	CellPickerInteractorStyle()
	{
		selectedMapper = vtkSmartPointer<vtkDataSetMapper>::New();
		selectedActor = vtkSmartPointer<vtkActor>::New();
	}
	virtual void OnLeftButtonDown()
	{
		int* pos = this->GetInteractor()->GetEventPosition();
		vtkSmartPointer<vtkCellPicker> picker =
			vtkSmartPointer<vtkCellPicker>::New();
		picker->SetTolerance(0.0005);
		picker->Pick(pos[0], pos[1], 0, this->GetDefaultRenderer());
 
		if (picker->GetCellId() != -1)
		{
			vtkSmartPointer<vtkIdTypeArray> ids =
				vtkSmartPointer<vtkIdTypeArray>::New();
			ids->SetNumberOfComponents(1);
			ids->InsertNextValue(picker->GetCellId());
 
			vtkSmartPointer<vtkSelectionNode> selectionNode =
				vtkSmartPointer<vtkSelectionNode>::New();
			selectionNode->SetFieldType(vtkSelectionNode::CELL);
			selectionNode->SetContentType(vtkSelectionNode::INDICES);
			selectionNode->SetSelectionList(ids);
 
			vtkSmartPointer<vtkSelection> selection =
				vtkSmartPointer<vtkSelection>::New();
			selection->AddNode(selectionNode);
 
			vtkSmartPointer<vtkExtractSelection> extractSelection =
				vtkSmartPointer<vtkExtractSelection>::New();
			extractSelection->SetInputData(0, polyData);
			extractSelection->SetInputData(1, selection);
			extractSelection->Update();
 
			selectedMapper->SetInputData((vtkDataSet*)extractSelection->GetOutput());
			selectedActor->SetMapper(selectedMapper);
			selectedActor->GetProperty()->EdgeVisibilityOn();
			selectedActor->GetProperty()->SetEdgeColor(1, 0, 0);
			selectedActor->GetProperty()->SetLineWidth(3);
 
			this->Interactor->GetRenderWindow()->GetRenderers()->GetFirstRenderer()->AddActor(selectedActor);
		}
		vtkInteractorStyleTrackballCamera::OnLeftButtonDown();
	}
public:
vtkSmartPointer<vtkPolyData>      polyData; 
private:
	
	vtkSmartPointer<vtkDataSetMapper> selectedMapper;
	vtkSmartPointer<vtkActor>         selectedActor;
};
/*********************************************************************************/
 
vtkStandardNewMacro(CellPickerInteractorStyle);
 
int main()
{
	vtkSmartPointer<vtkSphereSource> sphereSource =
		vtkSmartPointer<vtkSphereSource>::New();
	sphereSource->Update();
 
	vtkSmartPointer<vtkPolyDataMapper> mapper =
		vtkSmartPointer<vtkPolyDataMapper>::New();
	mapper->SetInputData(sphereSource->GetOutput());
 
	vtkSmartPointer<vtkActor> actor =
		vtkSmartPointer<vtkActor>::New();
	actor->GetProperty()->SetColor(0, 1, 0);
	actor->SetMapper(mapper);
 
	vtkSmartPointer<vtkRenderer> renderer =
		vtkSmartPointer<vtkRenderer>::New();
	renderer->AddActor(actor);
	renderer->SetBackground(1, 1, 1);
 
	vtkSmartPointer<vtkRenderWindow> rw =
		vtkSmartPointer<vtkRenderWindow>::New();
	rw->Render();
	rw->SetWindowName("CellPicker Interaction");
	rw->AddRenderer(renderer);
 
	vtkSmartPointer<vtkRenderWindowInteractor> rwi =
		vtkSmartPointer<vtkRenderWindowInteractor>::New();
	rwi->SetRenderWindow(rw);
/****************************************************************************/
	vtkSmartPointer<CellPickerInteractorStyle> style =
		vtkSmartPointer<CellPickerInteractorStyle>::New();
	style->SetDefaultRenderer(renderer);
	style->polyData = sphereSource->GetOutput();
	rwi->SetInteractorStyle(style);
 
	rw->Render();
	rwi->Initialize();
	rwi->Start();
	return 0;
}
————————————————
版权声明：本文为CSDN博主「沈子恒」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://shenchunxu.blog.csdn.net/article/details/54966221
```