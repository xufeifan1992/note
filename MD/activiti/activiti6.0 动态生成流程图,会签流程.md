# activiti6.0 动态生成流程图,会签流程



```java
package org.jeecg;

import org.activiti.bpmn.BpmnAutoLayout;
import org.activiti.bpmn.model.*;
import org.activiti.bpmn.model.Process;
import org.activiti.engine.TaskService;
import org.activiti.engine.delegate.DelegateExecution;
import org.activiti.engine.delegate.Expression;
import org.activiti.engine.impl.bpmn.behavior.MultiInstanceActivityBehavior;
import org.activiti.engine.impl.persistence.entity.ProcessDefinitionEntity;
import org.activiti.engine.repository.Deployment;
import org.activiti.engine.runtime.ProcessInstance;
import org.activiti.engine.task.Task;
import org.activiti.engine.test.ActivitiRule;
import org.apache.commons.io.FileUtils;
import org.junit.Rule;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * Created by xufei
 * 2019/6/27
 */
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class ActivitiTest {

    @Autowired
    private TaskService taskService;

    @Rule
    public ActivitiRule activitiRule = new ActivitiRule();

    @Value(value = "${jeecg.path.upload}")
    public String uploadpath;

    @Test
    public void testActivitiModel() throws IOException {

        BpmnModel bpmnModel = new BpmnModel();
        Process process = new Process();

        bpmnModel.addProcess(process);

        process.setId("userProcess");

        process.addFlowElement(createStartEvent("createEvent"));

        process.addFlowElement(createUserTask("task1", "处经理审核"));

        process.addFlowElement(createSequenceFlow("createEvent", "task1"));

        process.addFlowElement(createEndEvent("endEvent"));

        process.addFlowElement(createSequenceFlow("task1", "endEvent"));

        new BpmnAutoLayout(bpmnModel).execute();

        Deployment deployment = activitiRule.getRepositoryService().createDeployment()
                .addBpmnModel("userTaskTest.bpmn", bpmnModel).name("处经理审批流程")
                .deploy();

        Map<String,Object> map = new HashMap<>();
        List<String> assgineeList = new ArrayList<>();
        assgineeList.add("xuff1");
        assgineeList.add("xuff2");
        assgineeList.add("xuff3");
        map.put("assgineeList",assgineeList);


        ProcessInstance processInstance = activitiRule.getRuntimeService()
                .startProcessInstanceByKey("userProcess",map);


        InputStream processDiagram = activitiRule.getRepositoryService()
                .getProcessDiagram(processInstance.getProcessDefinitionId());

        FileUtils.copyInputStreamToFile(processDiagram, new
                File(uploadpath + "/diagram.png"));

    }

    /**
     * @param endEvent
     * @return
     */
    private EndEvent createEndEvent(String id) {
        EndEvent endEvent = new EndEvent();
        endEvent.setId(id);

        return endEvent;
    }

    /**
     * 创建连接线
     *
     * @param from
     * @param to
     * @return
     */
    private SequenceFlow createSequenceFlow(String from, String to) {
        SequenceFlow sequenceFlow = new SequenceFlow();
        sequenceFlow.setSourceRef(from);
        sequenceFlow.setTargetRef(to);

        return sequenceFlow;
    }

    /**
     * 创建用户任务
     *
     * @param task1
     * @param first_task
     * @param fred
     * @return
     */
    private UserTask createUserTask(String id, String name) {
        UserTask userTask = new UserTask();
        //创建多实例
        MultiInstanceLoopCharacteristics multiInstanceLoopCharacteristics = new MultiInstanceLoopCharacteristics();

        //设置为false为会签，true为顺签
        multiInstanceLoopCharacteristics.setSequential(false);
        //设置生成几个节点
        multiInstanceLoopCharacteristics.setLoopCardinality("3");
        //设置处理人集合
        multiInstanceLoopCharacteristics.setInputDataItem("assgineeList");
        //设置处理人
        multiInstanceLoopCharacteristics.setElementVariable("assginee");

        userTask.setLoopCharacteristics(multiInstanceLoopCharacteristics);
        //处理人变量
        userTask.setAssignee("#{assginee}");



        //List<String> assigneeList = new ArrayList<>();
        //assigneeList.add("xuff");
        //assigneeList.add("zhoushilin");
        //assigneeList.add("xiaojiji");
        userTask.setId(id);
        userTask.setName(name);
       // userTask.setCandidateUsers(assigneeList);
        return userTask;
    }

    /**
     * 创建开始Event
     *
     * @return
     */
    private StartEvent createStartEvent(String id) {
        StartEvent startEvent = new StartEvent();
        startEvent.setId(id);
        return startEvent;
    }

    @Test
    public void test(){
        List<Task> mary = taskService.createTaskQuery().taskAssignee("xuff1").list();
        System.out.println(mary);
    }

}


```

